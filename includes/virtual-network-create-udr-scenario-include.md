## Szenario

Um zu veranschaulichen, wie Sie benutzerdefinierte Routen erstellen, wird in diesem Dokument das folgende Szenario verwendet.

![BILDBESCHREIBUNG](./media/virtual-network-create-udr-scenario-include/figure1.png)

In diesem Szenario erstellen Sie eine UDR für die *Front-End-Subnetz* und anderen UDR für die *Back-End-Subnetz* , wie unten beschrieben: 

- **UDR-Front-End-**. Das Front-End UDR für gelten die *Front-End-* -Subnetz und enthalten eine Route:  
    - **RouteToBackend**. Diese Route sendet alle Datenverkehr an das Back-End-Subnetz für die **FW1** virtuellen Computer.
- **UDR-Back-End-**. Back-End UDR für gelten die *Back-End-* -Subnetz und eine Route enthalten: 
    - **RouteToFrontend**. Diese Route sendet alle Datenverkehr auf dem Front-End-Subnetz der **FW1** virtuellen Computer.

Die Kombination aus diesen Routen wird sichergestellt, dass alle Datenverkehr von einem Subnetz in ein anderes weitergeleitet wird die **FW1** virtuellen Computer, der als virtuelles Gerät verwendet wird. Sie müssen auch die IP-Weiterleitung für diesen virtuellen Computer aktivieren, damit er den an andere virtuelle Computer gerichteten Datenverkehr empfangen kann.


