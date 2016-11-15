<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>Konfigurera och registrera enheten
1. Gå in i Windows PowerShell-gränssnittet på din StorSimple-enhets seriekonsol. Mer instruktioner finns i [Använd PuTTY för att ansluta till enhetens seriekonsol](#use-putty-to-connect-to-the-device-serial-console). **Se till att följa proceduren exakt för att du ska få åtkomst till konsolen.**
2. I sessionen som öppnas, trycker du på Retur en gång för att få fram en kommandotolk. 
3. Du kommer att uppmanas att välja det språk som du vill ställa in för din enhet. Ange språk och tryck på Retur. 
   
    ![StorSimple konfigurera och registrera enhet 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. I menyn för seriekonsolen som visas, väljer du alternativ 1 för att logga in med fullständig åtkomst. 
   
    ![StorSimple registrera enhet 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     Slutför steg 5–12 för att konfigurera de minsta nödvändiga nätverksinställningarna för din enhet. **De här konfigurationsstegen behöver genomföras på den aktiva styrenheten för enheten.** Menyn för seriekonsolen indikerar status för styrenheten i banderollmeddelandet. Om du inte är ansluten till den aktiva styrenheten, kopplar du från och ansluter till den aktiva styrenheten.
5. Ange ditt lösenord i kommandotolken. Enheten standardlösenord är **Password1**.
6. Ange följande kommando:
   
     `Invoke-HcsSetupWizard` 
7. En installationsguide kommer visas och hjälpa dig att konfigurera nätverksinställningarna för enheten. Ange följande information: 
   
   * IP-adress för DATA 0-nätverksgränssnittet
   * Nätmask
   * Gateway
   * IP-adress för primär DNS-server
   * IP-adress för primär NTP-server
     
     > [!NOTE]
     > Du kan behöva vänta några minuter för att nätmask- och DNS-inställningarna ska appliceras. Om det kommer upp ett "enheten är inte redo." felmeddelande, kontrollerar du den fysiska nätverksanslutningen på DATA 0-nätverksgränssnittet på din aktiva styrenhet.
     > 
     > 
8. (Valfritt) konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfri, **var medveten om att om du använder en webbproxy så kan du bara konfigurera den här**. Mer information finns i [Konfigurera en webbproxy för din enhet](../articles/storsimple/storsimple-configure-web-proxy.md). Om du stöter på problem med det här steget, finns felsökningsanvisningar i [Fel under webbproxykonfigurationen](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).

     > [!NOTE]
     > Du kan trycka på Ctrl + C när som helst för att avsluta installationsguiden. Alla inställningar som du applicerade innan du anger kommandot kommer att sparas.

1. Av säkerhetsskäl upphör enhetens administratörslösenord att gälla efter den första sessionen. Du måste ändra lösenordet för efterföljande sessioner. Ange ett administratörslösenord för enheten när du ombes göra det. Ett giltigt enhetsadministratörslösenord för enheten måste vara mellan 8 och 15 tecken. Lösenordet måste innehålla en kombination av gemener, versaler, siffror och specialtecken.
2. Lösenordet för StorSimple Snapshot Manager ställs också in här. Du använder det här lösenordet när du autentiserar en enhet med din Windowsbaserade dator som kör StorSimple Snapshot Manager. När du uppmanas, ange ett lösenord på mellan 14 och 15 tecken. Lösenordet måste innehålla en kombination av tre av följande: gemener, versaler, siffror och specialtecken. 
   
   ![StorSimple registrera enhet 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   Du kan nollställa lösenordet för StorSimple Snapshot Manager från StorSimple Managers tjänstgränssnitt. Detaljerade anvisningar finns i [Ändra StorSimple-lösenord med hjälp av StorSimple Manager-tjänsten](../articles/storsimple/storsimple-change-passwords.md).
   
   Om du stöter på problem med det här steget, finns felsökningsanvisningar i [Fel relaterade till lösenord](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).
3. Det sista steget i installationsguiden registrerar din enhet med StorSimple Manager-tjänsten. För det här behöver du tjänstens registreringsnyckel som du fick i steg 2. Efter att du angett registreringsnyckeln kan du behöva vänta 2-3 minuter innan enheten är registrerad.
   
   För att felsöka eventuella fel med enhetsregistrering, referera till [Fel vid enhetsregistrering](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration). För detaljerad felsökning kan du även gå till [Stegvis felsökningsexempel](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).
4. När enheten är registrerad visas en krypteringsnyckel för tjänstdata. Kopiera den här nyckeln och spara den på säker plats.
   
   > [!WARNING]
   > Den här nyckeln kommer att krävas med registreringsnyckeln för tjänsten för att registrera ytterligare enheter med StorSimple Manager-tjänsten. Referera till [StorSimple-säkerhet](../articles/storsimple/storsimple-security.md) för ytterligare information om den här nyckeln.
   > 
   > 
   
    ![StorSimple registrera enhet 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    Om du vill kopiera text från seriekonsolfönstret, markerar du bara texten. Sedan ska du kunna klistra in den i urklipp eller valfri textredigerare. ANVÄND INTE Ctrl + C för att kopiera krypteringsnyckeln för tjänstdata. Ctrl + C avslutar installationsguiden. Som ett resultat, kommer enhetens administratörslösenord och lösenordet för StorSimple Snapshot Manager inte att ändras och enheten kommer återgå till standardlösenordet.
5. Avsluta seriekonsolen.
6. Gå tillbaka till den klassiska Azure-portalen och utför följande steg:
   
   1. Dubbelklicka på StorSimple Manager-tjänsten för att komma in på **Snabbstart**-sidan.
   2. Klicka på **Visa anslutna enheter**.
   3. På sidan **Enheter** kontrollerar du att enheten har lyckats ansluta till tjänsten genom att kontrollera dess status. Enhetens status ska vara **Online**. Om enhetens status är **Offline**, väntar du några minuter på att enheten ska ansluta.
   
   ![StorSimple-enhetssidan](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > När enheten är online, ansluter du nätverkskablarna som du hade dragit ut i början av det här steget.
   > 
   > 

När enheten har registrerats och inte är online, kan du köra `Test-HcsmConnection -Verbose` för att se till att nätverksanslutningen är felfri. För detaljerad användning av den här cmdleten, gå till [cmdlet-referens för Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx).

![Video tillgänglig](./media/storsimple-configure-and-register-device/Video_icon.png) **Video tillgänglig**

För att se på en video som visar hur du konfigurerar och registrerar din enhet via Windows PowerShell för StorSimple, klickar du [här](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).



<!--HONumber=Nov16_HO2-->


