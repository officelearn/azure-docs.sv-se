## Szenario

Um zu veranschaulichen, wie Sie NSGs erstellen, wird in diesem Dokument das folgende Szenario verwendet.

![VNet-Szenario](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

In diesem Szenario erstellen Sie eine NSG für jedes Subnetz in der **TestVNet** virtuellen Netzwerk, wie im folgenden beschrieben: 

- **NSG-Front-End-**. Das Front-End NSG für gelten die *Front-End-* Subnetz und zwei Regeln enthalten:  
    - **RDP-Regel**. Diese Regel ermöglicht die RDP-Datenverkehr an den *Front-End-* Subnetz.
    - **Web-Regel**. Diese Regel ermöglicht die HTTP-Datenverkehr an den *Front-End-* Subnetz.
- **NSG-Back-End-**. Das Back-End NSG für gelten die *Back-End-* -Subnetz und zwei Regeln enthalten: 
    - **SQL-Regel**. Mit dieser Regel können SQL-Verkehr nur von der *Front-End-* Subnetz.
    - **Web-Regel**. Diese Regel verweigert alle Internet gebunden Datenverkehr aus dem *Back-End-* Subnetz.

Durch die Kombination dieser Regeln wird ein DMZ-ähnliches Szenario erstellt, in dem das Back-End-Subnetz nur eingehenden Datenverkehr für SQL-Datenverkehr aus dem Front-End-Subnetz empfangen kann und keinen Zugriff auf das Internet hat, während das Front-End-Subnetz mit dem Internet kommunizieren und nur eingehende HTTP-Anforderungen empfangen kann.
 

