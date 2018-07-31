---
title: Prova en molnbaserad IoT-lösning för förutsägande underhåll i Azure | Microsoft Docs
description: I den här snabbstarten ska du distribuera Azure IoT-lösningsacceleratorn för förutsägande underhåll och logga in till och använda instrumentpanelen för lösningen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/12/2018
ms.author: dobett
ms.openlocfilehash: 10ff6565ed8997a5cb87394aa0d743a0d94b67e0
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144320"
---
# <a name="quickstart-try-a-cloud-based-solution-to-run-a-predictive-maintenance-analysis-on-my-connected-devices"></a>Snabbstart: Prova en molnbaserad lösning för att analysera behovet av förutsägande underhåll på anslutna enheter

I den här snabbstarten får du lära dig att distribuera Azure IoT-lösningsacceleratorn för förutsägande underhåll och simulera ett molnbaserat förutsägande underhåll. När du har distribuerat lösningsacceleratorn kan du analysera behovet av förebyggande underhåll utifrån data om en simulerad flygplansmotor på sidan **Instrumentpanel** i lösningen. Du kan använda den här lösningsacceleratorn som utgångspunkt för en egen implementering eller som utbildningsverktyg.

I den här simuleringen är Fabrikam ett regionalt flygbolag som fokuserar på bra kundupplevelser till konkurrenskraftiga priser. En orsak till flygförseningar är underhållsproblem, och flygplansmotorernas underhåll är särskilt krävande. Fabrikam måste till vilket pris som helst förhindra motorfel under flygningar och inspekterar därför regelbundet sina motorer och schemalägger underhåll med utgångspunkt i en plan. Flygplansmotorer slits dock inte alltid likadant. En del onödigt underhåll utförs på motorer. Dessutom kan det uppstå problem som gör att planet blir stående tills underhåll har utförts. Dessa problem bli extra kostsamma om ett flygplan finns på en plats där rätt tekniker eller reservdelar inte är tillgängliga.

Motorerna i Fabrikams flygplan är utrustade med sensorer som övervakar motortillståndet under flygning. Fabrikams dataspecialister har under flera år samlat in data om motorernas drift och eventuella fel och har tagit fram en modell som kan förutse flygplansmotorernas återstående livslängd. Modellen använder en korrelation mellan data från fyra av motorsensorerna och motorförslitningar som med tiden leder till motorfel. Fabrikam fortsätter att utföra regelbundna inspektioner för att säkerställa säkerheten, men nu kan man använda modeller för att beräkna varje motors återstående livslängd efter varje flygning. Fabrikam kan nu förutsäga framtida driftstopp och planera för underhåll för att minimera flygplanens marktid och minska driftkostnaderna samtidigt som man säkerställer passagerarnas och flygplansbesättningens säkerhet.

Du behöver en aktiv Azure-prenumeration för att kunna utföra den här snabbstarten.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="deploy-the-solution"></a>Distribuera lösningen

När du distribuerar lösningsacceleratorn till Azure-prenumerationen måste du ange några konfigurationsalternativ.

Logga in på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) med dina Azure-kontouppgifter.

Klicka på **Testa nu** på panelen **Förutsägande underhåll**.

![Välj Förutsägande underhåll](./media/quickstart-predictive-maintenance-deploy/predictivemaintenance.png)

På sidan **Create Predictive Maintenance solution** (Skapa lösning för förutsägande underhåll) anger du ett unikt **lösningsnamn** för utvecklingsacceleratorn för förutsägande underhåll. I den här snabbstarten använder vi **MyPredictiveMaintenance**.

Välj den **prenumeration** och den **region** du vill använda för att distribuera lösningsacceleratorn. Normalt väljer du regionen närmast dig. I den här snabbstarten använder vi **Visual Studio Enterprise** och **USA, östra**. Du måste vara [global administratör eller användare](iot-accelerators-permissions.md) i prenumerationen.

Klicka på **Skapa lösning** för att inleda distributionen. Processen tar minst fem minuter att köra:

![Information om lösningen för förutsägande underhåll](./media/quickstart-predictive-maintenance-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logga in på lösningen

När distributionen till Azure-prenumerationen är klar visas en grön bockmarkering och **Redo** på lösningspanelen. Nu kan du logga in på instrumentpanelen för lösningsacceleratorn för förutsägande underhåll.

Klicka på den nya lösningsacceleratorn för förutsägande underhåll på sidan **Etablerade lösningar**. Du kan visa information om lösningsacceleratorn på panelen som visas. Välj **Lösningens instrumentpanel** för att visa lösningsacceleratorn för förutsägande underhåll:

![Lösningens panel](./media/quickstart-predictive-maintenance-deploy/solutionpanel.png)

Acceptera behörighetsförfrågan genom att klicka på **Acceptera**. Nu visas instrumentpanelen för lösningen för förutsägande underhåll i webbläsaren:

![Instrumentpanel för lösningen](./media/quickstart-predictive-maintenance-deploy/solutiondashboard.png)

Starta simuleringen genom att klicka på **Starta simulering**. Instrumentpanelen fylls med sensorhistorik, RUL-värden, cykler och RUL-historik:

![Simuleringen körs](./media/quickstart-predictive-maintenance-deploy/simulationrunning.png)

Om RUL-värdet är mindre än 160 (ett godtyckligt tröskelvärde som valts som exempel) visas en varningssymbol på lösningsportalen bredvid RUL-värdet. Dessutom markeras flygplansmotorn i gult på lösningsportalen. Lägg märke till att RUL-värdena har en nedåtgående trend generellt, men med många upp- och nedgångar. Detta mönster beror på de olika cykellängderna och modellens precision.

![Simuleringsvarning](./media/quickstart-predictive-maintenance-deploy/simulationwarning.png)

Den fullständiga simuleringen tar 35 minuter för att slutföra 148 cykler. RUL-tröskelvärdet på 160 nås för första gången vid cirka 5 minuter och båda motorerna når tröskelvärdet ungefär vid 8 minuter.

Simuleringen kör igenom den fullständiga datauppsättningen i 148 cykler och bestämmer den slutliga återstående användbara livslängden och cykelvärdena.

Du kan stoppa simuleringen när du vill, men om du klickar på **Starta simulering** spelas simuleringen upp igen från början av datauppsättningen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill utforska ytterligare låter du lösningsacceleratorn för förutsägande underhåll förbli distribuerad.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att välja den och sedan klicka på **Ta bort lösning**:

![Ta bort lösningen](media/quickstart-predictive-maintenance-deploy/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat lösningsacceleratorn för förutsägande underhåll och kört en simulering.

Fortsätt till nästa artikel om du vill lära dig mer om lösningsacceleratorn och de simulerade flygplansmotorerna.

> [!div class="nextstepaction"]
> [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-walkthrough.md)
