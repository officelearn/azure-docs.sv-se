<!--author=alkohli last changed: 9/17/15-->

#### So führen Sie die mindestens erforderliche StorSimple-Gerätekonfiguration aus

1. In der **Geräte** Seite, wählen Sie das Gerät, klicken Sie auf den Pfeil für den Gerätenamen, um das Geräteseite aufzurufen. 

    ![Geräteseite mit online geschaltetem Gerät](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 

2. Klicken Sie auf Schnellstart-Symbol ![Quick Start-Symbol](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) Zugriff auf die Geräte-Schnellstart-Seite. Klicken Sie auf **Abschließen der Geräteinstallation** zum Starten der **Gerät konfigurieren** Assistenten.

    ![Schnellstartseite für das Gerät](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)

2. Auf der **Grundeinstellungen** Seite, gehen Sie folgendermaßen vor:
  1. Geben Sie einen **Anzeigenamen** für Ihr Gerät. Der Standardgerätename enthält Informationen zum Gerätemodell und zur Seriennummer. Sie können einen Anzeigenamen zuweisen, der bis zu 64 Zeichen lang ist, um Ihr Gerät zu verwalten.
  2. Legen Sie die **Zeitzone** basierend auf dem geografischen Standort, an dem das Gerät bereitgestellt wird. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.
  3. Unter **DNS-Einstellungen**, geben Sie eine Adresse für Ihre **sekundärer DNS-Server**. Wenn Sie IPv6 verwenden, wird das Feld basierend auf dem IPv6-Präfix ausgefüllt, das in der Windows PowerShell-Benutzeroberfläche angegeben wurde. 
  Wenn der sekundäre DNS-Server nicht konfiguriert ist, können Sie Ihre Gerätekonfiguration nicht speichern.
  4. Aktivieren Sie unter "Aktivierte iSCI Schnittstellen" mindestens ein Netzwerk für iSCSI. Mindestens eine Netzwerkschnittstelle muss cloudfähig sein, und eine Schnittstelle muss iSCSI-aktiviert sein. DATA 0 ist automatisch cloudfähig.
 
      ![Grundlegende Einstellungen für die minimale Konfiguration von StorSimple-Geräten](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Klicken Sie auf das Pfeilsymbol. ![StorSimple, Pfeilsymbol](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. Auf der **Netzwerkschnittstellen** Seite, die festen IP-Adressen für Controller 0 und Controller 1 bereitstellen. Wenn die DATA 0-Schnittstelle für IPv4 konfiguriert wurde, müssen die festen IP-Adressen im IPv4-Format angegeben werden. Wenn Sie ein Präfix für die IPv6-Konfiguration angegeben haben, werden die festen IP-Adressen in diesen Feldern automatisch mit Daten aufgefüllt.


    > [AZURE.NOTE] 
    > 
    > - Die festen IP-Adressen des Controllers müssen freie IP-Adressen in dem Subnetz sein, auf das die IP-Adresse des Geräts zugreifen kann.
    > - Die festen IP-Adressen für den Controller werden dazu verwendet, die Updates für das Gerät vorzunehmen. Daher ist es erforderlich, dass die festen IP-Adressen routingfähig und für Verbindungen mit dem Internet geeignet sind.

    ![StorSimple minimum device setup network interfaces](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

5. Klicken Sie auf das Häkchensymbol ![StorSimple, Häkchensymbol](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
  Sie gelangen zurück auf das Gerät **Schnellstart** Seite.

 > [AZURE.NOTE] Sie können alle anderen geräteeinstellungen jederzeit ändern, indem Sie den Zugriff auf die **konfigurieren** Seite.

![Video verfügbar](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **Video verfügbar**

Um ein Video ansehen, die veranschaulicht, wie Sie die mindestens erforderliche gerätesetup, klicken Sie auf [hier](http://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/).

