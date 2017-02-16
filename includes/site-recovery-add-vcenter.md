* I **Add vCenter** anger du ett eget namn för vSphere-värden eller vCenter-servern och sedan serverns IP-adress eller fullständiga domännamn. Lämna porten som 443 om inte dina VMware-servrar är konfigurerade för att lyssna efter begäranden på en annan port. Välj det konto som ska anslutas till VMware vCenter- eller vSphere ESXi-servern. Klicka på **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Om du lägger till VMware vCenter-servern eller VMware vSphere-värden med ett konto som inte har administratörsbehörigheter för vCenter- eller värdservern ska du se till att kontot har dessa behörigheter aktiverade: datacenter, datalager, mapp, värd, nätverk, resurs, virtuell dator och vSphere Distributed Switch. Dessutom behöver VMware vCenter-servern behörigheter för lagringsvisning aktiverad.


<!--HONumber=Feb17_HO2-->


