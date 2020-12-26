************************
Über diese Dokumentation
************************
Diese Dokumentation wird mithilfe von `ReadTheDocs <https://readthedocs.org/>`_ erstellt.

Dokumentation bearbeiten
=========================
.. note::
    Es wird empfohlen ein Python "`Virtual Environment <https://docs.python.org/3/library/venv.html>`_" anzulegen, um alle Abhängigkeiten sauber zu trennen. Dies geschieht mithilfe des ``venv``-Modules: 
    
    .. code-block:: console

        $ python -m venv venv

Um diese zu bearbeiten, muss das Git-Repository geklont und bspw. in Visual Studio Code geöffnet werden.

Nach dem Öffnen des Projektes sollte die "reStructuredText"-Erweiterung von LeXtudio Inc. zur Installation vorgeschlagen werden. Es müssen keine Zusatzfeatures, wie Linter aktiviert werden. Für Tabellen empfiehlt sich die Installation der "Table Formatter" Erweiterung von Shuzo Iwasaki.

Nach der Installation der RST-Erweiterung kann nach dem Öffnen einer entsprechenden RST-Datei oben rechts eine Preview nebenan geöffnet werden.

Mithilfe von ``make serve`` wird ein Web-Server auf dem lokalen Port 8000 gestartet, der automatisch die Dokumentation aktualisiert. So kann einfach am Design gearbeitet werden.
