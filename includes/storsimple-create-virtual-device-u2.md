#### Skapa en virtuell enhet

1.  I Azure-portalen går du till **StorSimple Manager**-tjänsten.

2. Gå till **Enheter**-sidan. Klicka på **Skapa virtuell enhet** längst ned på **Enheter**-sidan.

3. I **dialogrutan Skapa virtuell enhet** anger du följande information.

     ![StorSimple skapa virtuell enhet](./media/storsimple-create-virtual-device-u2/CreatePremiumsva1.png)

    1. **Namn** – ett unikt namn för din virtuella enhet.


    2. **Modell** – välj modell för den virtuella enheten. Det här fältet visas bara om du kör uppdatering 2 eller senare. Enhetsmodellen 8010 erbjuder 30 TB standardlagring medan 8020 har 64 TB premiumlagring. Välj 8010
    3.  för att distribuera hämtningsscenarier på objektsnivå från säkerhetskopior. Välj 8020 för att distribuera högpresterande arbetsbelastningar med låg latens eller som en sekundär enhet för haveriberedskap.
     
    4. **Version** – ange den virtuella enhetens version. Om en 8020 enhetsmodell väljs, visas inte versionsfältet för användaren. Det här alternativet kommer inte fram om alla fysiska enheter som är registrerade med den här tjänsten kör uppdatering 1 (eller senare). Det här fältet visas bara om du har en blandning av fysiska enheter registrerade från innan och efter uppdatering 1 på samma tjänst. Versionen av den virtuella enheten anger vilken fysisk enhet som du kan växla till eller klona från så det är viktigt att du skapar lämplig version av den. Välj:

       - Versionsuppdatering 0.3 om du växlar eller katastrofåterställer från en fysisk enhet som kör uppdatering 0.3 eller tidigare. 
       - Versionsuppdatering 1 om du växlar eller klonar från en fysisk enhet som kör uppdatering 1 (eller senare). 
       
    
    5. **Virtuellt nätverk** – Ange ett virtuellt nätverk som du vill använda med den här virtuella enheten. Om du använder premiumlagring (uppdatering 2 eller senare), måste du välja ett virtuellt nätverk som stöds av premium-lagringskontot. Virtuella nätverk som inte stöds är nedtonade i listrutan. Du får en varning om du väljer ett virtuellt nätverk som inte stöds. 

    5. **Lagringskonto för skapande av virtuell enhet** – välj ett lagringskonto som ska lagra avbildningen av den virtuella enheten under etableringen. Lagringskontot ska vara i samma region som den virtuella enheten och virtuella nätverket. Det ska inte användas för datalagring av vare sig den fysiska eller virtuella enheten. Ett nytt lagringskonto kommer som standard att skapas för det här ändamålet. Men om du vet att du redan har ett lagringskonto som är lämpligt, kan du välja det från listan. Om du skapar en premium virtuell enhet så kommer listmenyn bara att visa premium-lagringskonton. 

        >[AZURE.NOTE] Den virtuella enheten fungerar bara med Azure Storage-konton. Andra molntjänstleverantörer som Amazon, HP och OpenStack (som stöds för den fysiska enheten) stöds inte för den virtuella StorSimple-enheten.
    
    1. Klicka på kryssmarkeringen för att ange att du förstår att den data som lagras på den virtuella enheten kommer att finnas i ett Microsoft-datacenter. När du bara använder en fysisk enhet så sparas krypteringsnyckeln med din enhet. Microsoft kan därmed inte dekryptera den. 
     
        När du använder en virtuell enhet så lagras både krypteringsnyckeln och dekrypteringsnyckeln i Microsoft Azure. Mer information finns i [Säkerhetsöverväganden vid användning av en virtuell enhet](storsimple-security/#storsimple-virtual-device-security).
    2. Klicka på kryssikonen för att skapa den virtuella enheten. Enheten kan ta runt 30 minuter för att etableras.

    ![Skapandefas för virtuell StorSimple-enhet](./media/storsimple-create-virtual-device-u2/StorSimple_VirtualDeviceCreating1M.png)

    



<!--HONumber=Jun16_HO2-->


