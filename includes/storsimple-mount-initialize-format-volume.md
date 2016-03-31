<!--author=SharS last changed: 9/17/15-->

#### So stellen Sie ein Volume bereit und initialisieren und formatieren dieses

1. Starten Sie den Microsoft iSCSI-Initiator.

2. In der **iSCSI Initiator Properties** Fenster auf die **Ermittlung** auf **Portal ermitteln**.

3. In der **Zielportal** Dialogfeld Geben Sie die IP-Adresse Ihrer iSCSI-aktivierten Netzwerkschnittstelle, und klicken Sie dann auf **OK**. 

4. In der **iSCSI Initiator Properties** Fenster auf der **Ziele** Registerkarte der **Ziele ermittelt**. Der Status des Geräts sollte angezeigt werden, als **inaktiv**.

5. Wählen Sie das Zielgerät aus, und klicken Sie dann auf **Verbinden**. Nachdem das Gerät verbunden ist, den Status sollte nun **verbunden**. (Weitere Informationen zur Verwendung des Microsoft iSCSI-Initiators finden Sie unter [Installieren und Konfigurieren des Microsoft iSCSI-Initiator][1]).

6. Drücken Sie auf Ihrem Windows-Host die Windows-Logo-Taste + X, und klicken Sie dann auf **Ausführen**. 

7. In der **führen** Geben Sie im Dialogfeld **Diskmgmt.msc**. Klicken Sie auf **OK**, und die **Disk Management** Dialogfeld wird angezeigt. Im rechten Bereich werden die Volumes auf Ihrem Host angezeigt.

8. In der **Disk Management** Fenster, die bereitgestellten Volumes werden angezeigt, wie in der folgenden Abbildung dargestellt. Mit der rechten Maustaste des ermittelten Volumes (klicken Sie auf den Datenträgernamen), und klicken Sie dann auf **Online**.

     ![Initialisieren und Formatieren von Volumes](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 

9. Mit der rechten Maustaste des Volumes (klicken Sie auf den Datenträgernamen) erneut aus, und klicken Sie dann auf **initialisieren**.

10. Führen Sie die folgenden Schritte aus, um ein einfaches Volume zu formatieren.
  1. Wählen Sie das Volume, klicken Sie darauf (klicken Sie im rechten Bereich), und klicken Sie auf **Neues einfaches Volume**.
  2. Geben Sie im Assistenten für neue einfache Volumes die Volumegröße und den Laufwerkbuchstaben an, und konfigurieren Sie das Volume dann als Dateisystemtyp "NTFS".
  3. Geben Sie 64 KB als Zuordnungseinheitsgröße an. Diese Zuordnungseinheitsgröße eignet sich gut für die Deduplikationsalgorithmen, die in der StorSimple-Lösung verwendet werden.
  4. Führen Sie eine Schnellformatierung durch.

![Video verfügbar](./media/storsimple-mount-initialize-format-volume/Video_icon.png) **Video verfügbar**

Um ein Video ansehen, das zeigt, wie bereitstellen, initialisieren und Formatieren eines StorSimple-Volumes, klicken Sie auf [hier](http://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/).

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx


