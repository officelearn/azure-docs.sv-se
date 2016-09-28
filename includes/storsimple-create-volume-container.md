<!--author=SharS last changed: 9/17/15-->

#### Skapa en volymbehållare

1. På enhetens **Snabbstart**-sida klickar du på **Lägg till volymbehållare**. Dialogrutan **Skapa volymbehållare** visas.

    ![Skapa volymbehållare](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)

2. I dialogrutan **Skapa volymbehållare**:
  1. Ange ett **namn** för din volymbehållare. Namnet måste vara mellan 3 och 32 tecken.
  2. Ange ett **lagringskonto** att associera med volymbehållaren. Du kan välja det standardkonto som genereras när tjänsten skapas. Du kan också använda alternativet **Lägg till ny** för att ange ett lagringskonto som inte är länkad till den här tjänstprenumerationen.
  3. Välj **Aktivera kryptering av molnlagring** för att aktivera kryptering av data som skickas från enheten till molnet.
  4. Ange och bekräfta en **krypteringsnyckel för molnlagring** som är 8 till 32 tecken. Nyckeln används av enheten för att få åtkomst till krypterad data.
  5. Välj **Obegränsad** i listrutan **Ange bandbredd** om du vill använda all tillgänglig bandbredd. Du kan också välja alternativet **Anpassat** om du vill använda bandbreddskontroller, och sedan ange ett värde mellan 1 och 1 000 Mbps. 
  Om du har informationen om din bandbreddsanvändning tillgänglig, kan du allokera bandbredd enligt ett schema genom att ange **Välj en bandbreddsmall**. Stegvisa instruktioner för proceduren finns i [Lägg till en bandbreddsmall](storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template).
  6. Klicka på kryssikonen ![kryssikon](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) för att spara den här volymbehållaren och avsluta guiden. 

  Volymbehållaren som just skapades kommer att vara listad på sidan **Volymbehållare**.

![Video tillgänglig](./media/storsimple-create-volume-container/Video_icon.png) **Video tillgänglig**

För att se en video som visar hur du skapar en volymbehållare i din StorSimple-lösning, klickar du [här](https://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/).

<!--HONumber=Sep16_HO3-->


