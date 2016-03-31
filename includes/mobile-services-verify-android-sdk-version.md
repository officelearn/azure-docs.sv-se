Aufgrund ständiger Weiterentwicklung stimmt die Version des in Android Studio installierten Android-SDK unter Umständen nicht mit der Version im Code überein. In diesem Lernprogramm wird von Version 21 des Android-SDK ausgegangen. Das war die neueste Version zum Zeitpunkt der Verfassung. Mit neuen Ausgaben des SDK kann die Versionsnummer größer werden. Es empfiehlt sich, die neueste verfügbare Version zu benutzen.

Zwei Symptome nicht übereinstimmender Versionen sind:

1. Beim Erstellen oder das Projekt neu erstellen, erhalten Sie Gradle Fehlermeldungen wie "**Ziel Google dazu dass Fehler**".

2. Standard-Android-Objekte im Code, die auf Grundlage von `import`-Ausdrücken aufgelöst werden sollten, können Fehlermeldungen verursachen.

Wenn eines dieser Symptome auftritt, stimmt die Version des in Android Studio installierten Android-SDK möglicherweise nicht mit dem SDK-Ziel des heruntergeladenen Projekts überein.  Zur Überprüfung der Version nehmen Sie die folgenden Änderungen vor:


1. Klicken Sie in Android Studio **Tools** = > **Android** = > **SDK Manager**. Wenn nicht die neueste Version der SDK-Plattform installiert ist, so klicken Sie zur Installation. Notieren Sie sich die Versionsnummer.

2. Klicken Sie auf der Registerkarte Projekt-Explorer unter **Gradle Scripts**, öffnen Sie die Datei **build.gradle (Modeule: app)**. Sicherstellen, dass die **CompileSdkVersion** und **BuildToolsVersion** auf die neueste installierte SDK-Version festgelegt sind. Die Tags könnten folgendermaßen aussehen:
 
            compileSdkVersion 'Google Inc.:Google APIs:21'
            buildToolsVersion "21.1.2"
    
3. Im Projekt-Explorer von Android Studio Maustaste auf den Projektknoten, wählen **Eigenschaften**, und wählen Sie in der linken Spalte **Android**. Sicherstellen, dass die **Project Build Target** festgelegt ist, dieselbe SDK-Version als die **TargetSdkVersion**.

4. In Android Studio wird anders als bei Eclipse die Manifestdatei nicht mehr dazu verwendet, die Ziel-SDK und die SDK-Mindestversion anzugeben.


