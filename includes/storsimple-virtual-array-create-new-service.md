#### <a name="to-create-a-new-service"></a>Skapa en ny tjänst

1.  Logga in på Azure Portal på följande URL genom att ange autentiseringsuppgifterna för ditt Microsoft-konto: <https://portal.azure.com/>. Om distribuerar enheten i Government portal, logga in på: <https://portal.azure.us/>

2.  I Azure-portalen klickar du på **+ ny** &gt; **lagring** &gt; **virtuella StorSimple-serien**.

    ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  I den **StorSimple Enhetshanteraren** bladet som öppnas, gör du följande:

    1.  Ange ett unikt **resursnamn** för tjänsten. Resursnamnet är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 2 och 50 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

    2.  Välj en **prenumeration** från listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet syns inte om du bara har en prenumeration.

    3.  För **resursgruppen**väljer du en befintlig eller skapa en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Ange en **plats** för din tjänst. Se [Azure-regioner](https://azure.microsoft.com/regions/#services) för mer information om vilka tjänster som är tillgängliga i vilken region. I allmänhet ska du välja en **plats** närmast den geografiska region där du vill distribuera din enhet. Du kan också ta med följande i beräkningarna:

        -   Om du har befintliga arbetsbelastningar i Azure som du också tänker distribuera med din StorSimple-enhet, rekommenderar vi att du använder det datacentret.

        -   Din StorSimple-Enhetshanteraren och Azure-lagring kan vara två olika platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat. Du skapar ett Azure-lagringskonto genom att gå till Azure Storage-tjänsten på Azure Portal och följa stegen i [Skapa ett Azure Storage-konto](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account). När du har skapat kontot lägger du till det till StorSimple Device Manager-tjänsten genom att följa stegen i [Konfigurera ett nytt lagringskonto för tjänsten](https://azure.microsoft.com/en-us/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Om du distribuerar den virtuella enheten i Government Portal, är StorSimple Device Manager-tjänsten tillgänglig i Iowa för oss och vi Virginia platser.

    5.  Välj **skapar ett nytt Azure storage-konto** att automatiskt skapa ett lagringskonto med tjänsten. Ange en **lagringskontonamnet**. Avmarkera kryssrutan om du behöver ha din data på en annan plats.

    6.  Markera **Fäst på instrumentpanelen** om du vill skapa en snabblänk till tjänsten på instrumentpanelen.

    7.  Skapa StorSimple Device Manager genom att klicka på **Skapa**.

        ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Du dirigeras till den **Service** landningssida. Det tar några minuter att skapa tjänsten. När tjänsten har skapats, kommer du att meddelas och tjänstens status kommer att ändras till **Aktiv**.


