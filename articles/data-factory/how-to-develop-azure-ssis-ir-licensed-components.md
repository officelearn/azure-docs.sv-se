---
title: Installera licensierade komponenter för Azure-SSIS-integreringskörning
description: Lär dig hur en ISV kan utveckla och installera betalda eller licensierade anpassade komponenter för Azure-SSIS-integreringskörningen
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: b7079262dc7db4f4a00a9dc79193da1574c7153a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605861"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Installera betalda eller licensierade anpassade komponenter för Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur en ISV kan utveckla och installera betalda eller licensierade anpassade komponenter för SSIS-paket (SQL Server Integration Services) som körs i Azure i Azure-SSIS-integreringskörningen.

## <a name="the-problem"></a>Problemet

Vilken typ av Azure-SSIS-integreringskörning innebär flera utmaningar, vilket gör de typiska licensieringsmetoderna som används för lokal installation av anpassade komponenter otillräckliga. Därför kräver Azure-SSIS IR en annan metod.

-   Noderna i Azure-SSIS IR är flyktiga och kan allokeras eller släppas när som helst. Du kan till exempel starta eller stoppa noder för att hantera kostnaden, eller skala upp och ned genom olika nodstorlekar. Därför är det inte längre möjligt att binda en komponentlicens från tredje part till en viss nod med hjälp av maskinspecifik information som MAC-adress eller CPU-ID.

-   Du kan också skala Azure-SSIS IR in eller ut, så att antalet noder kan krympa eller expandera när som helst.

## <a name="the-solution"></a>Lösningen

Som ett resultat av begränsningarna för traditionella licensieringsmetoder som beskrivs i föregående avsnitt tillhandahåller Azure-SSIS IR en ny lösning. Den här lösningen använder Windows-miljövariabler och SSIS-systemvariabler för licensbindning och validering av tredjepartskomponenter. ISVs kan använda dessa variabler för att hämta unik och beständig information för en Azure-SSIS IR, till exempel kluster-ID och antal klusternoder. Med den här informationen kan ISV:er sedan binda licensen för sin komponent till en Azure-SSIS IR *som kluster*. Den här bindningen använder ett ID som inte ändras när kunder startar eller stoppar, skalar upp eller ned, skalar in eller ut eller konfigurerar om Azure-SSIS IR på något sätt.

I följande diagram visas typiska installations-, aktiverings- och licensbindnings- och valideringsflöden för komponenter från tredje part som använder dessa nya variabler:

![Installation av licensierade komponenter](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruktioner
1. ISV:er kan erbjuda sina licensierade komponenter i olika SKU:er eller nivåer (till exempel en nod, upp till 5 noder, upp till 10 noder och så vidare). ISV tillhandahåller motsvarande produktnyckel när kunder köper en produkt. ISV kan också tillhandahålla en Azure Storage-blob-behållare som innehåller ett ISV-installationsskript och associerade filer. Kunderna kan kopiera dessa filer till sin egen förvaringsbehållare och ändra `IsvSetup.exe -pid xxxx-xxxx-xxxx`dem med sin egen produktnyckel (till exempel genom att köra). Kunder kan sedan etablera eller konfigurera om Azure-SSIS IR med SAS URI för sin behållare som parameter. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md).

2. När Azure-SSIS IR etableras eller konfigureras om körs ISV-installationen på varje nod `SSIS_CLUSTERID` `SSIS_CLUSTERNODECOUNT`för att fråga windows-miljövariablerna och . Sedan skickar Azure-SSIS IR sitt kluster-ID och produktnyckeln för den licensierade produkten till ISV Activation Server för att generera en aktiveringsnyckel.

3. När du har fått aktiveringsnyckeln kan ISV-installationen lagra nyckeln lokalt på varje nod (till exempel i registret).

4. När kunder kör ett paket som använder ISV:s licensierade komponent på en nod i Azure-SSIS IR läser paketet den lokalt lagrade aktiveringsnyckeln och validerar det mot nodens kluster-ID. Paketet kan också rapportera antalet klusternoder till ISV-aktiveringsservern.

    Här är ett exempel på kod som validerar aktiveringsnyckeln och rapporterar antalet klusternoder:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV-partner

Du hittar en lista över ISV-partner som har anpassat sina komponenter och tillägg för Azure-SSIS IR i slutet av det här blogginlägget - [Enterprise Edition, Anpassad installation och utökningsbarhet för tredje part för SSIS i ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Nästa steg

-   [Anpassad konfiguration för Azure-SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
