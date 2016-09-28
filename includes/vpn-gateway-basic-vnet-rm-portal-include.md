Följ stegen nedan för att skapa ett VNet med Azure-portalen. Observera att skärmbilderna tillhandahålls som exempel. Se till att ersätta värdena med dina egna. Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.

2. Klicka på **Nytt** **>** **Nätverk** **>** **Virtuellt nätverk**.

    ![VNet-blad](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal650.png)

3. Nästan längst ned på Virtuellt nätverk-bladet, från **Välj en distributionsmodell**-listan, väljer du **Resource Manager** och klickar sedan på **Skapa**.


    ![Välj Resource Manager](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png)

4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. På det här bladet kommer du att lägga till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen. Det här är en begränsning i portalen för nuvarande. Du kan alltid gå tillbaka för att uppdatera de här värdena genom att redigtera VNet-egenskaperna i portalen, eller genom att använda PowerShell. De värden som du använder beror på den konfiguration du vill skapa. Se till att referera till dina planerade konfigurationsvärden. 

    ![Skapa ett virtuellt nätverksblad](./media/vpn-gateway-basic-vnet-rm-portal-include/createavnet250.png)

5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer med hjälp av listmenyn.

6. Klicka på **Resursgrupp** och välj antingen en befintlig resursgrupp, eller skapa en ny genom att ange ett namn för din nya resursgrupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](resource-group-overview.md#resource-groups).

7. Välj därefter **Plats**-inställningar för din VNet. Observera att platsen avgör var resurserna som du distribuerar till det här VNet kommer att finnas. Du kan inte ändra det här senare utan att omdistribuera dina resurser.

8. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.
    
    ![Fäst vid instrumentpanelen](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png)


9. När du klickar på Skapa, kommer du att se en ikon på din instrumentpanel som visar förloppet för ditt VNet. Ikonen kommer att ändra sig allteftersom ditt VNet skapas.

    ![Skapa en virtuell nätverksikon](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png)

<!--HONumber=Sep16_HO3-->


