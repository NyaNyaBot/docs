******************
Architekturkonzept
******************

Dieses Dokument beschreibt das **Architekturkonzept**.

Überblick
============

Was ist der nextbot?
------------------------
Der **nextbot** ist eine Bot-Software für den Telegram Messenger, der vollständig **auf Plugins basiert** und somit leicht erweiterbar ist. Entwickelt wird er seit November 2016 von Brawl und Centzilius, wobei letzterer den Grundstein für die Entwicklung gelegt und sich später zurückgezogen hat.

Was ist der Mikubot?
------------------------
**Mikubot** ist eine Abspaltung ("Fork") von nextbot, der auf die Bedürfnisse von Akamaru angepasst wurde. Upstream-Fixes werden zeitnah in das Projekt aufgenommen, wobei sich aber einige Probleme ergaben (siehe #2).

Was ist der NyaNyaBot?
--------------------------
Der NyaNyaBot ist die geplante Neuentwicklung des nextbot und wird in diesem Projekt beschrieben.

Gemeinsam genutzte Technologien
-----------------------------------
Die Bots sind geschrieben in der Programmiersprache **Python** und nutzen als Bibliothek zur Integration von Telegram "`python-telegram-bot <https://github.com/python-telegram-bot/python-telegram-bot>`_". Zur Datenspeicherung kommt eine **MySQL-Datenbank** zum Einsatz (bzw. Redis bei Mikubot).

Wieso eine neue Bot-Software?
---------------------------------
Es handelt sich beim NyaNyaBot um eine Neuentwicklung des **nextbot**, welcher seit drei Jahren online ist und mehrere hunderte Anfragen pro Tag bedient. Dabei traten einige Probleme auf, die - teils bedingt durch die Architektur - nicht oder nur durch enorm hohen Aufwand behoben werden könnten. Überwiegend handelt es sich dabei um **schlechte Designentscheidungen**, die sich erst Jahre später "rächten" oder durch Unerfahrenheit entstandene Bugs.

Mit der Veröffentlichung der `v13.0 <https://github.com/python-telegram-bot/python-telegram-bot/releases/tag/v13.0>`_ von python-telegram-bot wurden so viele Breaking Changes durchgeführt, dass eine Aktualisierung des *nextbot v3* nicht mehr tragbar ist.

Probleme
========

Perfomance
----------
Die Perfomance des Bots wird allgemein als **träge** empfunden - teils dauert es bis zu drei Sekunden, bis eine Abfrage beantwortet wurde. Mitunter hat dies auch mit der Verbindung zum Telegram-Server sowie eines Deadlocks zu tun, der auftritt, wenn sich der Bot wieder neu mit dem Server verbinden muss (insbesondere wenn eine neue Anfrage nach einiger Zeit reinkommt).

Fehlendes Feedback
------------------
Mitunter ist nicht ersichtlich, ob der Bot die Anfrage eines Nutzers immer noch beantwortet oder es zu einem Fehler kam. Oft fehlt Fehler-Handling oder es ist zu generisch gehalten, als dass es von Nutzen sein könnte. Wenn eine Anfrage noch etwas länger dauert, ist dies auch nicht immer ersichtlich.

Ungeregelte Datenbankzugriffe
-----------------------------
Die Datenbankeinbindung geschah über wenige Tage ohne genaue Planung, was zu Workaround-Entscheidungen führte, die fatal für das Projekt sind. Teils werden Datenbankverbindungen nicht geschlossen, was oft zu Abstürzen und Datenverlusten führt.

Uneinheitlicher Code
--------------------
Der Code von Plugins ist über mehrere Jahre zusammengekommen und wurde dabei nicht immer einheitlich refaktorisiert, was zu uneinheitlichem Code führte. Bspw. wurden Klassen eingeführt und anderer Stelle nicht benutzt, obwohl sie extra dafür gemacht wurden.

Legacy Code
-----------
Bedingt durch stetige Updates der API hat sich viel Legacy-Code angesammelt, der teilweise nicht mehr benötigt wird.

Forks
-----
Wie oben bereits angesprochen ist ein aktiver Fork von nextbot der *Mikubot* von Akamaru. Die Änderungen halten sich jedoch in Grenzen - lediglich geänderte Texte oder leicht geänderte Logging-Abfolgen im Core und neue Plugins. Oft ergeben sich dadurch schwere Merge-Konflikte und die MySQL-Anbindung wurde nie integriert, weshalb alle nachfolgenden Updates nicht mehr einfließen können.

Lösungen
=========

Den Nutzer informieren
----------------------
Es sollte deutlich mehr Feedback für den Nutzer eingebunden werden - so reicht oft schon eine simple Chat-Aktion, wie "*xyz tippt...*", die mit einer Zeile Code ausgelöst werden kann. Diese sollte immer am Anfang eines Plugins erfolgen.

Standardisierte Datenbank-Bibliothek
------------------------------------
Mithilfe einer standardisierten Datenbank-Bibliothek sollen Datenbankzugriffe halbautomatisiert passieren und zentral verwaltet werden. Es gibt bereits einen Prototypen des NyaNyaBots mit der Integration eines **Object-Relational-Mappers** (ORM) - einmal mit Peewee, einmal mit SQLAlchemy. Da sich bei der Benutzung aber einige Probleme abzeichneten, wurden diese Tests wieder eingestellt

Die Benutzung eines **Query Builders** wird analysiert, wobei folgende Kandidaten genauer unter die Lupe genommen werden sollen:

* `PyPika <https://github.com/kayak/pypika>`_
* `SQLAlchemy Core <https://docs.sqlalchemy.org/en/13/core/>`_

Linter
------
Um einheitlichen Code zu gewährleisten, haben sich sogenannte **Linter** bewährt, die den Code auf Bad Practices prüfen und stark konfigurierbar sind. Ideal ist die Integration in eine CI-Pipeline und in die IDE. Als Linter soll `Pylint <https://www.pylint.org/>`_ zum Einsatz kommen, welches sich auch gut in PyCharm `integrieren lässt <https://github.com/leinardi/pylint-pycharm/blob/master/README.md>`_.

Um Sicherheitsprobleme aufzuspüren soll der Einsatz von `Bandit <https://bandit.readthedocs.io/en/latest/>`_ evaluiert werden.

Konfigurierbarkeit
------------------
Um einen eventuellen Fork zu vermeiden sollen Grundfunktionalitäten stärker und einfacher konfigurierbar sein. Insbesondere Plugins sollten auch von einem **externen Pfad** geladen werden können, um keinen "Overlap" zu haben. Eventuell wäre es auch von Vorteil, Texte austauschbar zu machen, wobei hier der Kosten-Nutzen-Faktor abgewägt werden muss.

Dokumentation
-------------
Bisher existiert keine Dokumentation zur Bot-Software - dies soll sich ändern und alle Funktionen und Optionen, bis hin zum Programmablauf dokumentiert werden. Hierfür soll `Sphinx <https://docs.readthedocs.io/en/stable/intro/getting-started-with-sphinx.html>`_ mit RST zum Einsatz kommen.

CI-Pipeline
-----------
Durch eine **Continous Integration** Pipeline (bspw. mithilfe von GitHub Actions) sollen Fehler nach einem Commit direkt aufgespürt werden. Hier lässt sich bspw. auch Pylint integrieren. Die Pipeline soll auch die Dokumentation bauen und auf einer konfigurierbaren Domain veröffentlichen.

GitHub stärker nutzen
----------------------
GitHub und seine Features sollen stärker genutzt werden, insbesondere GitHub Actions (siehe 3.5). Noch zu erledigende Aufgaben sollen in Issues mit Tags getrackt und in Meilensteinen organisiert werden.

Development-Flow verbessern
---------------------------
Zurzeit werden alle Änderungen direkt in den master-Branch gepusht. Empfehlenswerter wäre es, alle Änderungen in einen **develop**-Branch zu pushen und nur getestete Änderungen in den master-Branch zu mergen. Für jedes Feature sollte ein neuer **feature**-Branch erstellt werden. Ferner soll der master-Branch geschützt und nur durch rebased Pull-Requests mit jeweils einem Feature pro PR beschreibbar sein.

Automatisierte Tests
--------------------
Das Testen einer flexiblen Bot-Software gestaltet sich eher als schwierig; dennoch sollten zumindest Tests der Core-Funktionen enthalten sein. Trotzdem sollten Plugin-Tests evaluiert werden; hierfür können die `Tests von python-telegram-bot <https://github.com/python-telegram-bot/python-telegram-bot/tree/master/tests>`_ zu Rate gezogen werden. Hier wird für Netzwerkanfragen bspw. `Flaky <https://github.com/box/flaky>`_ benutzt. Als Test-Software selbst soll `pytest <https://docs.pytest.org/en/stable/>`_ zum Einsatz kommen.

Funktionsumfang genauestens evaluieren
--------------------------------------
Der Bot sollte sich zuerst auf die meistgenutzten Grundfunktionen beschränken und alle Plugins sollten nach ihrem Nutzen evaluiert werden.

Benötigte Software
==================
+---------------------------+------------------------------------------+
|           Name            |                   Link                   |
+===========================+==========================================+
| Python >= v3.7            | https://www.python.org/                  |
+---------------------------+------------------------------------------+
| PyCharm Community Edition | https://www.jetbrains.com/de-de/pycharm/ |
+---------------------------+------------------------------------------+
| Visual Studio Code        | https://code.visualstudio.com/           |
+---------------------------+------------------------------------------+
| Git                       | https://git-scm.com/download/win         |
+---------------------------+------------------------------------------+

Aufbau und Struktur
===================

Code-Struktur
-------------
Der NyaNyaBot soll in mehrere Python-Pakete unterteilt werden, die alle unter dem "nyanyabot."-Paket unterteilt werden. Somit ergibt sich bspw. folgende Paketstruktur:

* nyanyabot
    * nyanyabot.core
    * nyanyabot.handler
    * nyanyabot.plugin

Plugin-API
----------
Die Plugin-API besteht aus einer Plugin-Klasse, die von Plugins erweitert wird. Sie stellt bspw. zentrales Logging und den Datenbank-Zugriff bereit.

Datenbank-Struktur
------------------
Folgende Datenbank-Struktur wird erwogen:

* **Bot-Daten:** bot\_\ *name*
* **Link-Tabellen:** lnk\_\ *tabelle1_entity1*\__\ *tabelle2_entity2*
* **Plugin-Daten:** plg\_\ *plugin_name*

Auf die Verwendung von Many-to-Many-Beziehungen mit Link-Tabellen soll so gut wie möglich verzichtet werden, da sie die Entwicklung unnötig verkomplizieren.