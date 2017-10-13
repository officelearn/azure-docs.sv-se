<!--author=alkohli last changed: 02/22/2016-->


### <a name="to-configure-and-register-the-device"></a>Konfigurera och registrera enheten
1. Gå in i Windows PowerShell-gränssnittet på din StorSimple-enhets seriekonsol. Mer instruktioner finns i [Använd PuTTY för att ansluta till enhetens seriekonsol](#use-putty-to-connect-to-the-device-serial-console). **Se till att följa proceduren exakt för att du ska få åtkomst till konsolen.**
2. I sessionen som öppnas, trycker du på Retur en gång för att få fram en kommandotolk. 
3. Du kommer att uppmanas att välja det språk som du vill ställa in för din enhet. Ange språk och tryck på Retur. 
   
    ![StorSimple konfigurera och registrera enhet 1](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice1-U1-include.png)
4. I menyn för seriekonsolen som visas, väljer du alternativ 1 för att logga in med fullständig åtkomst. 
   
    ![StorSimple registrera enhet 2](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice2_U1-include.png)
   
     Slutför steg 5–12 för att konfigurera de minsta nödvändiga nätverksinställningarna för din enhet. **De här konfigurationsstegen behöver genomföras på den aktiva styrenheten för enheten.** Menyn för seriekonsolen indikerar status för styrenheten i banderollmeddelandet. Om du inte är ansluten till den aktiva styrenheten, kopplar du från och ansluter till den aktiva styrenheten.
5. Ange ditt lösenord i kommandotolken. Enheten standardlösenord är **Password1**.
6. Ange följande kommando: `Invoke-HcsSetupWizard`. 
7. En installationsguide kommer visas och hjälpa dig att konfigurera nätverksinställningarna för enheten. Ange följande information: 
   
   * IP-adress för DATA 0-nätverksgränssnittet
   * Nätmask
   * Gateway
   * IP-adress för primär DNS-server
     
        Observera att systemet validerar nätverksinställningarna efter varje steg i processen.
     
     > [!NOTE]
     > Du kan behöva vänta några minuter för att nätmask- och DNS-inställningarna ska appliceras. Om du får ett felmeddelande om att "kontrollera nätverksanslutningen till Data 0", kontrollera den fysiska nätverksanslutningen på DATA 0-nätverksgränssnittet för din aktiva styrenhet.
     > 
     > 
8. (Valfritt) konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfri, **var medveten om att om du använder en webbproxy så kan du bara konfigurera den här**. Mer information finns i [Konfigurera en webbproxy för din enhet](../articles/storsimple/storsimple-configure-web-proxy.md).
9. Konfigurera en primär NTP-server för din enhet. NTP-servrar krävs, eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet. Om det inte är möjligt anger du en intern NTP-server. 
10. Av säkerhetsskäl upphör enhetens administratörslösenord att gälla efter den första sessionen. Du måste ändra lösenordet nu. Ange ett administratörslösenord för enheten när du ombes göra det. Ett giltigt enhetsadministratörslösenord för enheten måste vara mellan 8 och 15 tecken. Lösenordet måste innehålla tre av följande: gemener, versaler, siffror och specialtecken.
    
    <br/>![StorSimple registrera enhet 5](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice5_U1-include.png)
11. Det sista steget i installationsguiden registrerar din enhet med StorSimple Manager-tjänsten. För det här behöver du tjänstens registreringsnyckel som du fick i steg 2. Efter att du angett registreringsnyckeln kan du behöva vänta 2-3 minuter innan enheten är registrerad.
    
    > [!NOTE]
    > Du kan trycka på Ctrl + C när som helst för att avsluta installationsguiden. Om du har angett alla nätverksinställningar (IP-adress för Data 0, nätmask och Gateway), kommer informationen att sparas.
    > 
    > 
    
    ![StorSimple registrera enhet 6](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice6_U1-include.png)
12. När enheten är registrerad visas en krypteringsnyckel för tjänstdata. Kopiera den här nyckeln och spara den på säker plats. **Den här nyckeln kommer att krävas med registreringsnyckeln för tjänsten för att registrera ytterligare enheter med StorSimple Manager-tjänsten.** Referera till [StorSimple-säkerhet](../articles/storsimple/storsimple-security.md) för ytterligare information om den här nyckeln.
    
    ![StorSimple registrera enhet 7](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice7_U1-include.png)    
    
    > [!NOTE]
    > Om du vill kopiera text från seriekonsolfönstret, markerar du bara texten. Sedan ska du kunna klistra in den i urklipp eller valfri textredigerare. ANVÄND INTE Ctrl + C för att kopiera krypteringsnyckeln för tjänstdata. Ctrl + C avslutar installationsguiden. Som ett resultat, kommer enhetens administratörslösenord inte att ändras och enheten kommer återgå till standardlösenordet.
    > 
    > 
13. Avsluta seriekonsolen.
14. Gå tillbaka till den klassiska Azure-portalen och utför följande steg:
    
    1. Dubbelklicka på StorSimple Manager-tjänsten för att komma in på **Snabbstart**-sidan.
    2. Klicka på **Visa anslutna enheter**.
    3. På sidan **Enheter** kontrollerar du att enheten har lyckats ansluta till tjänsten genom att kontrollera dess status. Enhetens status ska vara **Online**.
       
        ![StorSimple-enhetssidan](./media/storsimple-configure-and-register-device-u1/HCS_DevicesPageM_U1-include.png) 
       
        Om enhetens status är **Offline**, väntar du några minuter på att enheten ska ansluta. 
       
        Om enheten fortfarande är offline efter några minuter, måste du kontrollera att ditt brandväggsnätverk har konfigurerats enligt beskrivningen i [nätverkskraven för din StorSimple-enhet](../articles/storsimple/storsimple-system-requirements.md). 
       
        Kontrollera att port 9354 är öppen för utgående kommunikation eftersom den används av service bus för StorSimple Manager-tjänsten för kommunikation till enheten.

