<!--author=SharS last changed: 1/7/2016-->

#### <a name="to-add-a-volume-container"></a>Att lägga till en volymbehållare
1. På den **enheter** sidan, väljer enheten, dubbelklicka på den och klicka sedan på den **volymbehållare** fliken.
2. Klicka på **Lägg till** längst ned på sidan. I den **skapa volymbehållare** dialogrutan Gör följande:
   
   1. Ange ett unikt **namn** för din volymbehållare. Det här namnet får innehålla högst 32 tecken.
   2. Välj en **Lagringskonto** som ska associeras med volymbehållaren. Du kan välja från ett befintligt lagringskonto i samma prenumeration eller välj **lägga till fler** att välja ett lagringskonto från en annan prenumeration. Du kan också välja det lagringskonto som skapades först när tjänsten har skapats.
   3. Ange bandbredd som **obegränsad** om du vill använda all tillgänglig bandbredd eller **anpassad** att använda bandbreddskontroller. Ange ett värde mellan 1 och 1000 Mbit/s för en anpassad bandbredd. Om du vill allokera bandbredd enligt ett schema, kan du **Välj en bandbreddsmall**.
   4. Vi rekommenderar att du behåller **aktivera Molnlagringskryptering** valt att kryptera data som kommer till molnet. Inaktivera kryptering endast om du använda andra sätt att kryptera dina data. Du kan inte ändra den kryptering inställningen när volymbehållaren har skapats.
   5. Ange en **krypteringsnyckel för Molnlagring** som innehåller mellan 8 och 32 tecken. Enheten använder den här nyckeln för att få åtkomst till krypterade data. I den **bekräfta krypteringsnyckel för Molnlagring** , ange krypteringsnyckeln för molnlagring igen för att bekräfta den. 
   6. Klicka på pilen för att fortsätta till nästa sida.
      
      ![Skapa volymbehållare med bandbreddsmallen 1](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 
3. Om du har angett **Välj en bandbreddsmall**, Välj i listrutan för befintliga bandbreddsmallar. Granska schemainställningarna och klicka på kryssikonen ![kryssikon](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).
   
    ![Skapa volymbehållare med bandbreddsmallen 2](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

Volymcontainern kommer att sparas och den nya volymbehållaren visas på den **volymbehållare** sidan.

