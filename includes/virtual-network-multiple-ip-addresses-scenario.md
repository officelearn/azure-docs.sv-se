## <a name="scenario"></a>Scenario
En virtuell dator med en enda nätverkskort skapas och ansluten till ett virtuellt nätverk. Den virtuella datorn kräver tre olika *privata* IP-adresser och två *offentliga* IP-adresser. IP-adresser tilldelas följande IP-konfigurationer:

* **IPConfig-1:** tilldelar en *statiska* privat IP-adress och en *statiska* offentlig IP-adress.
* **IPConfig-2:** tilldelar en *statiska* privat IP-adress och en *statiska* offentlig IP-adress.
* **IPConfig-3:** tilldelar en *statiska* privat IP-adress och ingen offentlig IP-adress.
  
    ![Flera IP-adresser](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

IP-konfigurationer har associerats med nätverkskortet när NIC skapas och nätverkskortet som är kopplad till den virtuella datorn när den virtuella datorn skapas. Typerna av IP-adresser som används för scenariot är illustration. Du kan tilldela IP-adress och tilldelning av typer som du behöver.

> [!NOTE]
> Om stegen i den här artikeln tilldelar alla IP-konfigurationer till ett enda nätverkskort, kan du också tilldela flera IP-konfigurationer till alla NIC på en virtuell dator flera nätverkskort. Information om hur du skapar en virtuell dator med flera nätverkskort, läsa den [skapa en virtuell dator med flera nätverkskort](../articles/virtual-network/virtual-network-deploy-multinic-arm-ps.md) artikel.