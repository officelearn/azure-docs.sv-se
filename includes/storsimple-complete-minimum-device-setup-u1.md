<!--author=alkohli last changed: 9/17/15-->

#### So führen Sie die mindestens erforderliche StorSimple-Gerätekonfiguration aus

1. Wählen Sie das Gerät, und klicken Sie auf **Schnellstart**. Klicken Sie auf **Abschließen der Geräteinstallation** um den Assistenten zum Konfigurieren von Geräten zu starten.

2. In den Assistenten zum Konfigurieren von Geräten **grundlegende Einstellungen** Dialogfeld Feld, gehen Sie folgendermaßen vor:
  1. Geben Sie einen **Anzeigenamen** für Ihr Gerät. Der Standardgerätename enthält Informationen zum Gerätemodell und zur Seriennummer. Sie können einen Anzeigenamen zuweisen, der bis zu 64 Zeichen lang ist, um Ihr Gerät zu verwalten.
  2. Legen Sie die **Zeitzone** basierend auf dem geografischen Standort, an dem das Gerät bereitgestellt wird. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.
  3. Unter **DNS-Einstellungen**, geben Sie eine Adresse für Ihre **sekundärer DNS-Server**. Wenn Sie IPv6 verwenden, wird das Feld basierend auf dem IPv6-Präfix ausgefüllt, das in der Windows PowerShell-Benutzeroberfläche angegeben wurde. 
  Wenn der sekundäre DNS-Server nicht konfiguriert ist, können Sie Ihre Gerätekonfiguration nicht speichern.
  4. Aktivieren Sie unter "Aktivierte iSCI Schnittstellen" mindestens ein Netzwerk für iSCSI. Mindestens eine Netzwerkschnittstelle muss cloudfähig sein, und eine Schnittstelle muss iSCSI-aktiviert sein. DATA 0 ist automatisch cloudfähig.
 
      ![Grundlegende Einstellungen für die minimale Konfiguration von StorSimple-Geräten](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Klicken Sie auf das Pfeilsymbol. ![StorSimple, Pfeilsymbol](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. In der **Netzwerkschnittstellen** Dialogfeld geben die festen IP-Adressen für Controller 0 und Controller 1. **Die festen IP-Adressen des Controllers müssen freie IP-Adressen in dem Subnetz sein, auf das die IP-Adresse des Geräts zugreifen kann.** Wenn die DATA 0-Schnittstelle für IPv4 konfiguriert wurde, müssen die festen IP-Adressen im IPv4-Format angegeben werden. Wenn Sie ein Präfix für die IPv6-Konfiguration angegeben haben, werden die festen IP-Adressen in diesen Feldern automatisch mit Daten aufgefüllt.


    ![StorSimple minimum device setup network interfaces](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    The fixed IP addresses for the controller are used for servicing the updates to the device, and therefore the fixed IPs must be routable and able to connect to the Internet. You can check that your fixed controller IPs are routable by using the [Test-HcsmConnection][Test] cmdlet. The following example shows fixed controller IPs are routed to the Internet and can access the Microsoft Update servers. 

     ![Test-HcsmConnection showing routable IPs](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

5. Klicken Sie auf das Häkchensymbol ![StorSimple, Häkchensymbol](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
  Sie gelangen zurück auf das Gerät **Schnellstart** Seite.

 > [AZURE.NOTE] Sie können alle anderen geräteeinstellungen jederzeit ändern, indem Sie den Zugriff auf die **konfigurieren** Seite.

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx

