Deutsch | [English](README_en.md) | [HowTo's: Inhaltsverzeichnis](https://github.com/toafez/Tutorials)

## HowTo: Der Benutzer-Home-Dienst des Synology DiskStation Managers
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Ftoafez%2FHowTo_Syno.DSM.User.Home&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

## Worum geht es
Die folgende Anleitung beschreibt einerseits die Einrichtung und die Besonderheiten des Benutzer-Home-Dienstes des DiskStation Managers, aber auch die Unterschiede, wie Synology mit Home-Verzeichnissen im Vergleich zu anderen Linux-Betriebssystemen umgeht.

#### _Hinweis: Texte in Großbuchstaben innerhalb eckiger Klammern dienen als Platzhalter und müssen durch eigene Angaben ersetzt werden, können aber an einigen Stellen auch nur der Information dienen. Es ist zu beachten, dass die eckigen Klammern Teil des Platzhalters sind und beim Ersetzen durch eigene Angaben ebenfalls entfernt werden müssen._

## Einleitung
Linux ist als **Mehrbenutzersystem** konzipiert und dementsprechend für die Nutzung durch mehrere Benutzer optimiert. Aus diesem Grund erhält jeder Benutzer ein **persönliches Verzeichnis** mit vollen Schreib- und Leserechten, in dem er seine eigenen Dateien sowie benutzerspezifische Daten und Einstellungen ablegen kann, **das sogenannte Home- oder Heimatverzeichnis**.

Dieses Home-Verzeichnis befindet sich normalerweise direkt unter dem Wurzelverzeichnis (/) und heißt `/home`. Innerhalb des Home-Verzeichnisses befinden sich wiederum Verzeichnisse, die nach den angelegten Benutzern benannt sind. Lautet der Name eines Benutzers beispielsweise _tommes_, so befindet sich sein Heimatverzeichnis unter dem Pfad `/home/tommes`. Häufig wird in diesem Zusammenhang auch die Kurzform `~/` verwendet. Das hier verwendete **Tilde**-Zeichen (**~**) stellt eine Abkürzung dar, das immer auf das Heimatverzeichnis `/home/[BENUTZERNAME]` verweist. Möchte man zum Beispiel von irgendeinem beliebigen Punkt innerhalb des Dateisystems in das Verzeichnis `/home/[BENUTZERNAME]/Dokumente` wechseln, ginge das auch über den Befehl `cd ~/Dokumente`

- #### Ein Beispiel
  Nach dem Einloggen in die grafische Oberfläche eines beliebigen Linux-Betriebssystems und dem Start eines Terminal-Programms wird man auf der Konsole in das eigene Home-Verzeichnis weitergeleitet (erkennbar am Benutzernamen vor dem @-Zeichen und dem Tilde-Zeichen innerhalb der Eingabeaufforderung (auch Prompt genannt) `tommes@Ubuntu-Server:~$`. Durch Eingabe des Befehls `pwd` (steht für print working directory) kann man sich den Pfad ausgeben lassen, in dem man sich befindet.

  ```
  tommes@Linux-Client:~$ pwd
  /home/tommes
  ```
  Durch Eingabe des Befehls `cd /` (cd steht für change directory) wird in das Wurzelverzeichnis des Dateisystems gewechselt. Eine erneute Eingabe des Befehls `pwd` bestätigt dies. Die Eingabeaufforderung ändert sich ebenfalls und das Tilde-Zeichen wird durch einen Schrägstrich / ersetzt, der das Wurzelverzeichnis anzeigt.

  ```
  tommes@Linux-Client:~$ cd /
  tommes@Linux-Client:/$ pwd
  /
  ```
  Durch Eingabe des Befehls `cd ~/` wechselt man wieder zurück in das eigene Home-Verzeichnis. Auch dies kann man sich durch die Eingabe des Befehls `pwd` bestätigen lassen.

  ```
  tommes@Linux-Client:/$ cd ~/
  tommes@Linux-Client:~$ pwd
  /home/tommes
  ```
## Der Benutzer-Home-Dienst des Synology DiskStation Managers
Mit dem ebenfalls auf Linux basierenden DiskStation Manager weicht **Synology** von der Struktur der allgemein üblichen Linux-Home-Verzeichnisse ab, da der DiskStation Manager (kurz DSM) eine eigene Benutzerverwaltung mitbringt, die zunächst unabhängig vom eingangs beschriebenen Linux-Mehrbenutzersystem ist, auch wenn im Hintergrund nach den gleichen Prinzipien verfahren wird. Vereinfacht ausgedrückt werden in der Standardkonfiguration des DSM zunächst keine Homeverzeichnisse wie gerade gezeigt angelegt, diese können aber jederzeit über die Systemsteuerung des DSM nachträglich aktiviert und genutzt werden.

Im Folgenden wird die Einrichtung und Nutzung des Benutzer-Home-Dienstes erläutert und auf die Unterschiede und Besonderheiten der Ordner `/home` und `/homes` sowie der Verzeichnisse `/var/services/homes` und `/root` eingegangen.

- ### Ausgangslage
  Wie eingangs erwähnt, werden in der Standardkonfiguration des DSM zunächst keine Home-Verzeichnisse angelegt. Dies lässt sich leicht überprüfen, da im **DSM** unter **Systemsteuerung > Freigegebene Ordner** kein Ordner mit dem Namen **home** vorhanden ist. Auch in der File Station wird der Ordner in der linken Seite der Ordnerübersicht nicht angezeigt und wenn man sich mit einem Terminalprogramm über SSH an der Konsole des DSM anmeldet, wird man u.a. mit folgender Meldung begrüßt:

  `Could not chdir to home directory /var/services/homes/[BENUTZERNAME]: No such file or directory`

  Es war also nicht möglich, in das Home-Verzeichnis des aktuell angemeldeten SSH-Benutzers zu wechseln, da dieses Verzeichnis nicht existiert. Interessanterweise wird in der Meldung als Ziel nicht auf den eingangs erwähnten Ordner `/home/[BENUTZERNAME]` verwiesen, sondern auf den Pfad `/var/services/homes/[BENUTZERNAME]`. Dazu später mehr.

- ### Benutzer-Home-Dienst aktivieren
  Um die Home-Verzeichnisse nutzen zu können, muss der **Benutzer-Home-Dienst** aktiviert werden. Melde dich dazu am DSM deines Synology NAS mit einem Konto an, das zur Gruppe der Administratoren (administrators) gehört. Navigiere anschließend zu **DSM-Hauptmenü > Systemsteuerung > Benutzer und Gruppe _(1)_** und wechsle dort auf die Registerkarte **Erweitert _(2)_**. Aktiviere unter dem Menüpunkt **Benutzerbasis** das Kontrollkästchen **Benutzer-Home-Dienst aktivieren _(3)_**. Direkt darunter kann noch der **homes-Speicherort _(4)_** ausgewählt werden, also das Volume, auf dem die Home-Verzeichnisse der Benutzer gespeichert werden sollen. Die Vorauswahl verweist hier zunächst auf das `/volume1` und kann bei Bedarf angepasst werden, wenn mehrere Volumes zur Auswahl stehen.

  ![01_DSM_User_and_Group](/images/01_DSM_User_and_Group.png)

  Nach der Aktivierung des Benutzer-Home-Dienstes wurde ein neuer **freigegebener Ordner** mit dem Namen **homes** auf dem zuvor angegebenen Volume angelegt welcher ab sofort auch unter **Systemsteuerung > Freigegebene Ordner** angezeigt wird.

  ![02_DSM_Shared_Folder](/images/02_DSM_Shared_Folder.png)

  Dieser Ordner erscheint auch in der File Station auf der linken Seite der Ordnerübersicht. Wenn du mit der rechten Maustaste auf den Ordner **homes _(1)_** klickst und im erscheinenden Kontextmenü den Punkt **Eigentschaften _(2)_** auswählst, wird dir der Pfad in der Registerkarte **Allgemein _(3)_** unter Ort angezeigt. In diesem Beispiel lautet der Pfad `/volume1/homes`

  ![03_DSM_FileStation_homes](/images/03_DSM_FileStation_homes.png)

  Zusätzlich erscheint in der File Station direkt über dem Ordner **homes** ein weiterer Ordner mit dem Namen **home**. Interessanterweise taucht dieser vermeintliche _freigegebene_ Ordner jedoch nicht unter **Systemsteuerung > Freigegebene Ordner** auf. Wenn du in der File Station auf der linken Seite der Ordnerübersicht mit der rechten Maustaste auf den Ordner **home** klickst, wirst du feststellen, dass im angezeigten Kontextmenü der Punkt Eigenschaften nicht verfügbar ist.

  ![04_DSM_FileStation_home](/images/04_DSM_FileStation_home.png)

  Dies erscheint auf den ersten Blick ziemlich merkwürdig, daher wird im Folgenden der Unterschied zwischen `/home` und `/homes` näher erläutert.

## Der Ordner /homes
Der Ordner **homes** bzw. das Verzeichnis `/volume[x]/homes` ist das Gegenstück zum unter Linux üblichen Home-Verzeichnis `/home`. Im Verzeichnis `/volume[x]/homes` befinden sich, wie unter Linux üblich, wiederum Verzeichnisse, die nach den im DSM angelegten Benutzern benannt sind. Wenn man sich nun mit einem Terminalprogramm über SSH an der Konsole des DSM anmeldet wird man feststellen, das einem die Meldung `Could not chdir to home directory /var/services/homes/[BENUTZERNAME]: No such file or directory` nicht mehr angezeigt wird, da man sich nun im Home-Verzeichnis des angemeldeten Benutzer befindet.

***_Aber Moment mal!_*** Wieso befindet man sich auf der Konsole nun im Home-Verzeichnis `/var/services/homes/[BENUTZERNAME]` und nicht unter `/volume[x]/homes/[BENUTZERNAME]`? Jetzt wird es für den unbedarften Benutzer ein wenig knifflig, weil das Verzeichnis `/var/services/homes` nur ein symbolischer Link auf das Verzeichnis `/volume[x]/homes` ist. Wenn man also versucht, auf den symbolischen Link `/var/services/homes/[BENUTZERNAME]` zuzugreifen, greift man tatsächlich auf den Pfad zu, auf den der symbolische Link verweist, nämlich `/volume[x]/homes/[BENUTZERNAME]`. Hintergrund des Ganzen ist, dass im DSM die Home-Verzeichnisse nicht wie bei Linux üblich direkt unter dem Wurzelverzeichnis (/) im Ordner `/home` liegen, sondern auf einem frei wählbaren Volume unter `/volume[x]/homes`. Der symbolische Link stellt somit sicher, dass auf der Konsole alle Zugriffe auf das Home-Verzeichnis an die richtige Stelle weitergeleitet werden.

**_Zur Wiederholung:_** Die unter Linux standardmäßig gruppierten Home-Verzeichnisse liegen auf einem Synology NAS nicht wie üblich unter `/home`, sondern unter `/volume[x]/homes`. Damit auf der DSM-Konsole der Bezug zu diesem Ordner nicht verloren geht, wird im Verzeichnis `/var/services/homes` ein symbolischer Link zu diesem Ordner erstellt. Auf der Konsole lässt sich der symbolische Link über den Befehl `ls -l` anzeigen.

```
root@SynologyNAS:/var/services# ls -l
total 0
lrwxrwxrwx+ 1 root root 14 Nov 26 17:00 homes -> /volume1/homes
```
#### _Hinweis: Alle Benutzer, die zur Gruppe der Administratoren (administrators) gehören, haben somit immer Zugriff auf die persönlichen Ordner aller Benutzer. Allen anderen Benutzern wird der Ordner **homes** nicht angezeigt und der Zugriff darauf verweigert._

## Der Ordner /home
Wenn du am DSM deines Synology NAS mit einem Konto angemeldet bist, das zur Gruppe der Administratoren gehört, wird in der File Station neben dem Ordner **homes** auch der Ordner **home** angezeigt, andernfalls bekommst du nur den Ordner **home** angezeigt. 

Wenn du, wie weiter oben bereits erwähnt, in der File Station auf der linken Seite der Ordnerübersicht mit der rechten Maustaste auf den Ordner **home** klickst, wird dir im angezeigten Kontextmenü der Punkt Eigenschaften nicht angezeigt. Der Grund dafür ist, dass der persönliche Benutzerordner **home** gar nicht existiert. Stattdessen wird für jeden im DSM angemeldeten Benutzer ein persönlicher **virtueller** sowie **temporärer** Ordner mit dem Namen **home** angezeigt, der sich aber tatsächlich unter `/volume[x]/homes/[BENUTZERNAME]` befindet und wie ein symbolischer Link darauf verweist. Das Verzeichnis **home** ist jedoch das eigentliche Arbeitsverzeichnis für jeden am DSM angemeldeten Benutzer, einschließlich Benutzer aus der Gruppe der Administratoren. 

**_Merke:_** Änderungen im Ordner **homes** sollten nur aus administrativen Gründen oder zu Sicherungs- und Wiederherstellungszwecken vorgenommen werden, ansonsten sollte stats der persönliche Ordner **home** verwendet werden.

## Der Ordner /root
Der Superuser **root** wiederum hat ein eigenes Home-Verzeichnis außerhalb der Benutzer-Home-Verzeichnisse, das direkt unter dem Wurzelverzeichnis (/) liegt und `/root` heißt. Auf dieses Verzeichnis kann jedoch nicht über die grafische Benutzeroberfläche des DSM zugegriffen werden, sondern nur über ein Terminalprogramm, das über eine SSH- oder Telnet-Verbindung auf die Konsole des DSM zugreift.

## Eine Gemeinsamkeit bleibt - das Tilde-Zeichen (~)
Trotz aller Unterschiede und Widrigkeiten bleibt dem Benutzer der schnelle Wechsel ins Home-Verzeichnis durch die Verwendung des Tilde-Zeichens erhalten, mit dem Unterschied, dass man auf der Konsole des DiskStation Managers durch die Eingabe von `cd ~/` im Verzeichnis `/var/services/homes/[BENUTZERNAME]` landet und eben nicht im Ordner `/home/[BENUTZERNAME]`. Dadurch wird das Arbeiten an der Konsole nicht unnötig erschwert. Immerhin!
