1. Logga in på [Azure Portal](https://portal.azure.com).

2. Börja längst upp till vänster och klicka på **Nytt > Beräkna > Windows Server 2016 Datacenter**.

    ![Gå till Azure VM-avbildningarna på portalen](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. Välj den klassiska distributionsmodellen i Windows Server 2016 Datacenter. Klicka på Skapa.

    ![Skärmbild som visar de tillgängliga avbildningarna för virtuella Azure-datorer på portalen](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Bladet Grundläggande inställningar

På bladet Grundläggande inställningar anger du administrativ information för den virtuella datorn.

1. Ange ett **namn** för den virtuella datorn. I exemplet är _HeroVM_ namnet på den virtuella datorn. Namnet måste vara mellan 1 och 15 tecken långt och får inte innehålla specialtecken.

2. Ange ett **användarnamn** och ett starkt **lösenord** som ska användas för att skapa ett lokalt konto på den virtuella datorn. Det lokala kontot används för att logga in på och hantera den virtuella datorn. I exemplet är _azureuser_ användarnamnet.

 Lösenordet måste innehålla mellan 8 och 123 tecken och uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken. Läs mer om [krav för användarnamn och lösenord](../articles/virtual-machines/windows/faq.md).

3. **Prenumeration** är valfritt. En vanlig inställning är ”Betala per användning”.

4. Välj en befintlig **resursgrupp** eller skriv namnet på en ny. I exemplet är _HeroVMRG_ namnet på resursgruppen.

5. Välj en **plats** för Azure-datacentret där du vill att den virtuella datorn ska köras. I exemplet är **USA, östra** platsen.

6. När du är klar fortsätter du till nästa blad genom att klicka på **Nästa**.

    ![Skärmbild som visar bladet Grundläggande inställningar där du kan konfigurera en virtuell Azure-dator](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Bladet Storlek

På bladet Storlek visas konfigurationsdetaljerna för den virtuella datorn samt en lista över olika alternativ, t.ex. operativsystem, antal processorer, typ av disklagring och beräknade användningskostnader per månad.  

Välj en storlek för den virtuella datorn och fortsätt genom att klicka på **Välj**. I exemplet är _DS1_\__V2 Standard_ VM-storleken.

  ![Skärmbild av bladet Storlek som visar de tillgängliga storlekarna för virtuella datorer i Azure](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Bladet Inställningar

På bladet Inställningar anger du alternativ för lagring och nätverk. Du kan acceptera standardinställningarna. Azure skapar relevanta poster om det behövs.

Om du valde en VM-storlek som stöder det kan du prova Azure Premium Storage genom att välja Premium (SSD) i Disktyp.

När du har gjort önskade ändringar klickar du på **OK**.

## <a name="4-summary-blade"></a>4. Bladet Sammanfattning

Bladet Sammanfattning innehåller en lista över de inställningar som du angav på det föregående bladet. Klicka på **OK** när du är redo att skapa avbildningen.

 ![Bladet Sammanfattning med angivna inställningar för den virtuella datorn](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

När du har skapat den virtuella datorn visas den virtuella datorn på portalen under **Alla resurser**, och en panel visas för den virtuella datorn på instrumentpanelen. Den tillhörande molntjänsten och det associerade lagringskontot skapas och visas också. Både den virtuella datorn och molntjänsten startas automatiskt och deras status visas som **Körs**.

 ![Konfigurera VM-agenten och slutpunkterna för den virtuella datorn](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
