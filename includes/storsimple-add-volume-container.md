<!--author=SharS last changed: 1/7/2016-->

#### <a name="to-add-a-volume-container"></a>Att lägga till en volymbehållare
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **volymbehållare** fliken.
2. Klicka på **Lägg till** längst ned på sidan. I den **skapa volymbehållare** dialogrutan Gör följande:
   
   1. Ange ett unikt **namn** för din volymbehållare. Namnet får innehålla högst 32 tecken.
   2. Välj en **Lagringskonto** som ska associeras med volymbehållaren. Du kan välja ett befintligt lagringskonto i samma prenumeration eller välj **lägga till fler** att välja ett lagringskonto från en annan prenumeration. Du kan också välja lagringskontot som genererades först när tjänsten har skapats.
   3. Ange bandbredd som **obegränsad** om du vill använda all tillgänglig bandbredd eller **anpassad** att använda bandbreddskontroller. Ange ett värde mellan 1 och 1000 Mbit/s för en anpassad bandbredd. Om du vill allokera bandbredd enligt ett schema kan du **Välj en bandbreddsmall**.
   4. Vi rekommenderar att du behåller **aktivera molnet Lagringskryptering** har valt att kryptera data som skickas till molnet. Inaktivera kryptering endast om du använder andra sätt att kryptera data. Du kan inte ändra den kryptering inställningen när volymbehållaren har skapats.
   5. Ange en **krypteringsnyckel för Molnlagring** som innehåller mellan 8 och 32 tecken. Enheten använder den här nyckeln för att få åtkomst till krypterade data. I den **bekräfta krypteringsnyckel för Molnlagring** , ange krypteringsnyckeln för molnlagring igen för att bekräfta den. 
   6. Klicka på pilen för att gå vidare till nästa sida.
      
      ![Skapa volymbehållare med bandbreddsmall 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 
3. Om du har angett **Välj en bandbreddsmall**, Välj från den nedrullningsbara listan med befintliga mallar för bandbredd. Granska inställningar för schemaläggning och klicka på kryssikonen ![kryssikon](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).
   
    ![Skapa volymbehållare med bandbreddsmall 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

Volymbehållaren ska sparas och den nyligen skapade volymbehållaren kommer att visas på den **volymbehållare** sidan.

