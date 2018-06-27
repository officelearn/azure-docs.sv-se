<!--author=SharS last changed: 02/22/2016-->

### <a name="to-configure-and-register-the-device"></a>Konfigurera och registrera enheten
1. Gå in i Windows PowerShell-gränssnittet på din StorSimple-enhets seriekonsol. Mer instruktioner finns i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console). **Se till att följa proceduren exakt för att du ska få åtkomst till konsolen.**
2. I sessionen som öppnas, trycker du på Retur en gång för att få fram en kommandotolk.
3. Du kommer att uppmanas att välja det språk som du vill ställa in för din enhet. Ange språk och tryck på Retur.
   
    ![StorSimple konfigurera och registrera enhet 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. I menyn för seriekonsolen som visas, väljer du alternativ 1, **logga in med fullständig åtkomst**.
   
    ![StorSimple registrera enhet 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Utför följande steg om du vill konfigurera de minsta nödvändiga nätverksinställningarna för enheten.
   
   > [!IMPORTANT]
   > De här konfigurationsstegen behöver genomföras på den aktiva styrenheten för enheten. Menyn för seriekonsolen indikerar status för styrenheten i banderollmeddelandet. Om du inte är ansluta till den aktiva styrenheten, kopplar du från och ansluter sedan till den aktiva styrenheten.
   > 
   > 
   
   1. Ange ditt lösenord i kommandotolken. Enheten standardlösenord är **Password1**.
   2. Ange följande kommando:
      
        `Invoke-HcsSetupWizard`
   3. En installationsguide kommer visas och hjälpa dig att konfigurera nätverksinställningarna för enheten. Ange följande information:
      
      * IP-adress för DATA 0-nätverksgränssnittet
      * Nätmask
      * Gateway
      * IP-adress för primär DNS-server
      * IP-adress för primär NTP-server
      
      > [!NOTE]
      > Du kan behöva vänta några minuter för nätmask och DNS-inställningar som ska användas.
      > 
      > 
   4. Alternativt kan du konfigurera din webbproxyserver.
      
      > [!IMPORTANT]
      > Även om webbproxykonfigurationen är valfri, Tänk på att om du använder en webbproxy, du kan bara konfigurera den här. Mer information finns i [Konfigurera en webbproxy för din enhet](../articles/storsimple/storsimple-configure-web-proxy.md).
      > 
      > 
6. Tryck på Ctrl + C om du vill avsluta installationsguiden.
7. Installera uppdateringar på följande sätt:
   
   1. Använd följande cmdlet för att ange IP-adresser på båda domänkontrollanterna:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. I Kommandotolken, kör `Get-HcsUpdateAvailability`. Du får ett meddelande att uppdateringar är tillgängliga.
   3. Kör `Start-HcsUpdate`. Du kan köra det här kommandot på en nod. Uppdateringarna tillämpas på den första domänkontrollanten, styrenheten växlar och sedan uppdateringarna tillämpas på en annan domänkontrollant.
      
      Du kan övervaka förloppet för uppdateringen genom att köra `Get-HcsUpdateStatus`.    
      
      Följande exempel på utdata visar att uppdateringen pågår.
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ````
      
      Följande exempel på utdata visar att uppdateringen är färdig.
      
      ```
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ```
      
      Det kan ta upp till 11 timmar att tillämpa alla uppdateringar, inklusive Windows-uppdateringar.
8. Kör följande cmdlet för att peka enheten på Microsoft Azure Government-portalen (eftersom den pekar till offentliga Azure klassiska portal som standard). Både domänkontrollanter kommer att startas om. Vi rekommenderar att du använder två PuTTY sessioner samtidigt ansluta till båda domänkontrollanterna så att du kan se när varje styrenhet har startats om.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Visas ett bekräftelsemeddelande. Acceptera standardvärdet (**Y**).
9. Kör följande cmdlet för att fortsätta installationsprogrammet:
   
    `Invoke-HcsSetupWizard`
   
    ![Återuppta installationsguiden](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
   När du fortsätter installationen kommer guiden att uppdatering 2-version.
10. Acceptera nätverksinställningarna. Ett verifieringsmeddelande visas när du har accepterat varje inställning.
11. Av säkerhetsskäl upphör enhetens administratörslösenord att gälla efter den första sessionen. Du måste ändra lösenordet nu. Ange ett administratörslösenord för enheten när du ombes göra det. Ett giltigt enhetsadministratörslösenord för enheten måste vara mellan 8 och 15 tecken. Lösenordet måste innehålla tre av följande: gemener, versaler, siffror och specialtecken.
    
    <br/>![StorSimple registrera enhet 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. Det sista steget i installationsguiden registrerar din enhet med StorSimple Manager-tjänsten. För detta behöver du tjänstens registreringsnyckel som du fick i [steg 2: Hämta nyckel för tjänstregistrering](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Efter att du angett registreringsnyckeln kan du behöva vänta 2-3 minuter innan enheten är registrerad.
    
    > [!NOTE]
    > Du kan trycka på Ctrl + C när som helst för att avsluta installationsguiden. Om du har angett alla nätverksinställningar (IP-adress för Data 0, nätmask och Gateway), kommer informationen att sparas.
    > 
    > 
    
    ![StorSimple registrering pågår](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. När enheten är registrerad visas en krypteringsnyckel för tjänstdata. Kopiera den här nyckeln och spara den på säker plats. **Den här nyckeln kommer att krävas med registreringsnyckeln för tjänsten för att registrera ytterligare enheter med StorSimple Manager-tjänsten.** Referera till [StorSimple-säkerhet](../articles/storsimple/storsimple-security.md) för ytterligare information om den här nyckeln.
    
    ![StorSimple registrera enhet 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > Om du vill kopiera text från seriekonsolfönstret, markerar du bara texten. Sedan ska du kunna klistra in den i urklipp eller valfri textredigerare.
    > 
    > ANVÄND INTE Ctrl + C för att kopiera krypteringsnyckeln för tjänstdata. Ctrl + C avslutar installationsguiden. Som ett resultat, kommer enhetens administratörslösenord inte att ändras och enheten kommer återgå till standardlösenordet.
    > 
    > 
14. Avsluta seriekonsolen.
15. Gå tillbaka till den offentliga Azure-portalen och utför följande steg:
    
    1. Dubbelklicka på StorSimple Manager-tjänsten för att komma in på **Snabbstart**-sidan.
    2. Klicka på **Visa anslutna enheter**.
    3. På sidan **Enheter** kontrollerar du att enheten har lyckats ansluta till tjänsten genom att kontrollera dess status. Enhetens status ska vara **Online**.
       
        ![StorSimple-enhetssidan](./media/storsimple-configure-and-register-device-gov-u2/HCS_DeviceOnline-gov-include.png)
       
        Om enhetens status är **Offline**, väntar du några minuter på att enheten ska ansluta.
       
        Om enheten fortfarande är offline efter några minuter, måste du kontrollera att ditt brandväggsnätverk har konfigurerats enligt beskrivningen i [nätverkskraven för din StorSimple-enhet](../articles/storsimple/storsimple-system-requirements.md).
       
        Kontrollera att port 9354 är öppen för utgående kommunikation eftersom den används av service bus för StorSimple Manager-tjänsten för kommunikation till enheten.

