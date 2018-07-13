## <a name="scenario"></a>Scenario
Det här dokumentet beskriver en distribution som använder en statisk offentlig IP-adress som har allokerats till en virtuell dator (VM). I det här scenariot har du en enskild virtuell dator med sin egen statisk offentlig IP-adress. Den virtuella datorn är en del av ett undernät med namnet **klientdel** och även har en statisk privat IP-adress (**192.168.1.101**) i det undernätet.

Du kan behöva en statisk IP-adress för webbservrar som kräver SSL-anslutningar som SSL-certifikatet är länkad till en IP-adress. 

![BESKRIVNING AV AVBILDNING](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Du kan följa stegen nedan för att distribuera den miljö som visas i bilden ovan.

