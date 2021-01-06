***************
Bot-Entwicklung
***************

Entwicklungsumgebung aufsetzen
==============================
Wie schon im Architekturkonzept erwähnt, wird PyCharm als IDE empfohlen. Hier muss lediglich das Git-Repositorium geklont und mit PyCharm geöffnet werden.

.. note::
    Es wird empfohlen ein Python "`Virtual Environment <https://docs.python.org/3/library/venv.html>`_" anzulegen, um alle Abhängigkeiten sauber zu trennen. Dies geschieht mithilfe des ``venv``-Modules: 
    
    .. code-block:: console

        $ python -m venv venv

    oder direkt über PyCharm.

    Um das Virtualenv zu aktivieren:

    .. code-block:: console

        $ venv/scripts/activate
    
    Letzteres geschieht in PyCharm automatisch, wenn der entsprechende Python-Interpreter gewählt wird (wird i.d.R. vorgeschlagen).

Die Abhängigkeiten können entweder über PyCharm, oder über die Konsole installiert werden:

.. code-block:: console

    $ pip install -U -r requirements.txt
    $ pip install -U -r requirements-dev.txt

error: Microsoft Visual C++ 14.0 or greater is required.
--------------------------------------------------------

Wenn die Fehlermeldung ``error: Microsoft Visual C++ 14.0 or greater is required. Get it with "Microsoft C++ Build Tools"`` erscheint, wird **nicht empfohlen**, dies zu installieren. Diese Fehlermeldung bedeutet lediglich, dass kein Binary-Wheel vorhanden ist, was insbesondere bei *mysqlclient* und seinen Abhängigkeiten (*greenlet*, etc.) vorkommen kann.

Stattdessen sollte das aktuelle Wheel von der "`University of California, Irvine <https://www.lfd.uci.edu/~gohlke/pythonlibs/>`_" heruntergeladen und über die Kommandozeile installiert werden:

.. code-block:: console

    $ pip install "mysqlclient‑1.4.6‑cp39‑cp39‑win_amd64.whl"

*cp* gibt hierbei die Python-Version an, *amd64* die System-Architektur (64-Bit).

Zusätzliche Einstellungen
=========================
Unter ``File | Settings | Tools | Python Integrated Tools`` muss *Docstring format* auf ``Google`` umgestellt werden.

Plugins einrichten
==================
Folgende Plugins müssen installiert werden:

+--------+---------------------------------------------------+
|  Name  |                       Link                        |
+========+===================================================+
| Mypy   | https://plugins.jetbrains.com/plugin/11086-mypy   |
+--------+---------------------------------------------------+
| Pylint | https://plugins.jetbrains.com/plugin/11084-pylint |
+--------+---------------------------------------------------+

Mypy
----
`Mypy <http://mypy-lang.org/>`_ ist ein Static-Type-Checker für Python, der die Typen alle Parameter und Funktionen auf ihre Richtigkeit prüft. Nach der Installation des Mypy-Plugins kann in den PyCharm-Einstellungen der Pfad zur Mypy-Binary angegeben werden, wobei folgende Einstellungen zu treffen sind:

* **Path to mypy executable:** ``venv\Scripts\mypy``
* **Path to config file:** ``.mypy.ini``
* **Arguments:** *<leer>*

Hier sind jeweils die vollständigen Pfade einzutragen und für Windows die EXE zu wählen. Zusätzlich wird strengstens empfohlen, unter "Inspections" Mypy auszuschalten, da die Real-Time-Analyse die Perfomance der IDE stark einschränkt.

Danach kann unten in der IDE der "Mypy"-Tab gefunden und aktiviert werden. Hier lässt sich sowohl die ganze Datei, das gesamte Modul, als auch nur die geänderten Dateien überprüfen.

Vor einem Commit kann an der rechten Seite auch die neue Checkbox zum Überprüfen mit Mypy angehakt werden.

Pylint
------
`Pylint <https://www.pylint.org/>`_ dient der strengen Einhaltung von Coding-Standards. Nach der Installation des Plugins kann dieses in den PyCharm-Einstellungen konfiguriert werden, wobei folgende Einstellungen zu treffen sind:

* **Path to pylint executable:** ``venv\Scripts\pylint``
* **Path to config file:** ``.pylintrc``
* **Arguments:** *<leer>*

Hier sind jeweils die vollständigen Pfade einzutragen und für Windows die EXE zu wählen. Zusätzlich wird strengstens empfohlen, unter "Inspections" Pylint auszuschalten, da die Real-Time-Analyse die Perfomance der IDE stark einschränkt.

Danach kann unten in der IDE der "Pylint"-Tab gefunden und aktiviert werden. Hier lässt sich sowohl die ganze Datei, das gesamte Modul, als auch nur die geänderten Dateien überprüfen.

Vor einem Commit kann an der rechten Seite auch die neue Checkbox zum Überprüfen mit Pylint angehakt werden.

Datenbank
=========
Eine MySQL-kompatible Datenbank wird benötigt; empfohlen wird MariaDB. Es muss lediglich eine Datenbank erstellt werden, wobei die Standard-Kollation auf ``utf8mb4_unicode_ci`` festgelegt werden sollte. Die Datenbank-Einstellungen können in der :doc:`Konfiguration <configuration>` vorgenommen werden.

Konfigurationsdatei anpassen
============================
Die ``config.json.example`` muss nach ``config.json`` kopiert und angepasst werden (siehe :doc:`Konfiguration <configuration>`).

Starten
=======
Um den Bot zu starten muss oben rechts in PyCharm eine neue Run-Configuration hinzugefügt werden. Hier reicht lediglich, von "*Script path*" auf "*Module name*" umzustellen und ``nyanyabot`` einzugeben. Zusätzliche Parameter lassen sich im Eingabefeld darunter übergeben. I.d.R. reicht es, hier ``..\config.json`` einzugeben, um die config.json aus dem übergeordneten Pfad zu laden.

Code einchecken
===============
Der stabile Branch ist ``master``; auf diesen kann nur über Pull-Requests gepusht werden. Der Standard-Branch ist ``develop`` und auf diesen sollte sich die gesame Entwicklung beziehen. Wird an einem neuen Feature gearbeitet, **muss** ein neuer Branch aus diesem mit dem Namen ``feature/1-mein-feature`` erstellt werden, wobei die Angabe der Issue-Nummer (hier ``1``) strengstens empfohlen wird.

Sobald das Feature fertig, getestet und als stabil empfunden wird, muss ein Pull-Request gegen den ``develop``-Branch erstellt werden. Der Code dieses Branches wird in Produktion beim Brawlbot eingesetzt und geprüft. Treten über einen längeren Zeitraum keine Fehler auf, wird der Code in den ``master``-Branch übernommen.
