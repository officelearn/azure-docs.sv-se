<!--author=alkohli last changed:01/14/2016-->


#### <a name="to-create-a-new-service"></a>Skapa en ny tjänst
1. Med autentiseringsuppgifterna för ditt Microsoft-konto kan logga in på den klassiska Azure-portalen på denna URL: [ https://manage.windowsazure.com/ ](https://manage.windowsazure.com/).
2. I den klassiska Azure-portalen klickar du på **Ny** > **Data Services** > **StorSimple Manager** > **Snabbregistrering**.
3. I formuläret som visas gör du följande:
   
   1. Ange ett unikt **namn** för din tjänst. Det är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 2 och 50 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.
   2. Ange en **plats** för din tjänst. I allmänhet ska du välja en plats som är närmast den geografiska region där du vill distribuera din enhet. Du kan också ta med följande i beräkningarna: 
      
      * Om du har befintliga arbetsbelastningar i Azure som du också tänker distribuera med din StorSimple-enhet, bör du använda det datacentret.
      * Din StorSimple Manager-tjänst och Azure-lagring kan finnas på två olika platser. I sådana fall måste du skapa StorSimple Manager-kontot och Azure-lagringskontot separat. För att skapa ett Azure-lagringskonto går du till Azure Storage-tjänsten i den klassiska Azure-portalen och följer stegen i [Skapa ett Azure Storage-konto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account). När du skapat det här kontot, lägger du till det till StorSimple Manager-tjänsten genom att följa stegen i [konfigurera ett nytt lagringskonto för tjänsten](../articles/storsimple/storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).
   3. Välj en **prenumeration** från listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet syns inte om du bara har en prenumeration.
   4. Välj **Skapa ett nytt lagringskonto**, för att automatiskt skapa ett lagringskonto med tjänsten. Det här lagringskontot har ett särskilt namn, till exempel "storsimplebwv8c6dcnf." Avmarkera kryssrutan om du behöver ha din data på en annan plats. 
   5. Klicka på **Skapa StorSimple Manager** för att skapa tjänsten.
   
   ![Skapa StorSimple Manager](./media/storsimple-create-new-service/HCS_CreateAService-include.png)
   
   Du kommer nu omdirigeras till **Tjänst**-landningssidan. Det tar några minuter att skapa tjänsten. När tjänsten har skapats, kommer du att meddelas och tjänstens status kommer att ändras till **Aktiv**.
   
   ![Skapa en tjänst](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Video tillgänglig](./media/storsimple-create-new-service/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du skapar en ny StorSimple Manager-tjänst klickar du [här](https://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/).

