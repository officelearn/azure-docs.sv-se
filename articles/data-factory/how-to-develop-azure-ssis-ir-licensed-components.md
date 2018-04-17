---
title: Utveckla betald eller licensierade komponenter för Azure-SSIS-integrering runtime | Microsoft Docs
description: Den här artikeln beskrivs hur en ISV kan utveckla och installera betald eller licensierad anpassade komponenter för Azure-SSIS-integrering runtime
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Utveckla betald eller licensierade anpassade komponenter för Azure-SSIS-integrering runtime

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>Problem - Azure SSIS-IR kräver en annan identifieringsmetod

Komponenternas Azure SSIS-integrering runtime presenterar flera utmaningar som gör de vanliga licensiering metoder som används för lokal installation av anpassade komponenter som saknas.

-   Noder i Azure-SSIS-IR är temporär och kan tilldelat eller publicerat när som helst. Du kan till exempel starta eller stoppa noder för att hantera kostnaden eller skala upp eller ned via olika storlekar på noden. Därför är bindning en tredjepartskomponent licens till en viss nod med hjälp av datorspecifik information, till exempel MAC-adress eller CPU-ID inte längre giltigt.

-   Du kan även skala Azure SSIS-IR in eller ut, så att antalet noder kan krympa eller expandera när som helst.

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>Lösningen – Windows-miljövariabler och SSIS systemvariabler för licensens bindning och validering

På grund av begränsningar av traditionella licensiering metoder som beskrivs i föregående avsnitt ger Azure SSIS-IR Windows-miljövariabler och SSIS systemvariabler för licensens bindning och verifiering av komponenter från tredje part. ISV: er kan använda dessa variabler för att hämta information om unika och beständiga för en Azure-SSIS-IR, t.ex ID för kluster och antalet noder för kluster. Med den här informationen ISV: er kan binda licensen för sina komponenten till en Azure-SSIS-IR *som ett kluster*, med ett ID som inte ändras när kunder starta eller stoppa skala upp eller ned, skala in eller ut eller konfigurera om Azure-SSIS-IR på något sätt.

Följande diagram visar standardinstallation, aktivering och licensens bindning och validering flödar för tredjeparts-komponenter som använder dessa nya variabler:

![Installationen av licensierade komponenter](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruktioner
1. ISV kan erbjuda sina licensierade komponenter i olika SKU: er eller nivåer (till exempel enkel nod, upp till 5 noder, upp till 10 noder och så vidare). ISV: er ger motsvarande produktnyckeln när kunder köpa en produkt. ISV: er kan också ge en Azure Storage blob-behållare som innehåller en ISV installationsskriptet och tillhörande filer. Kunder kan kopiera filerna till sina egna lagringsbehållaren och ändra dem med sina egna produktnyckel (till exempel genom att köra `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Kunder kan sedan etablera eller konfigurera om Azure-SSIS-IR med deras behållare som parameter SAS-URI. Mer information finns i [anpassad installation för Azure-SSIS-integrering runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. När Azure SSIS-IR etablerats eller omkonfigureras, ISV-installationen körs på varje nod för att söka i Windows-miljövariablerna `SSIS_CLUSTERID` och `SSIS_CLUSTERNODECOUNT`. Azure-SSIS-IR skickar sedan dess ID för kluster och produktnyckeln för den licensierade produkten Aktiveringsservern ISV för att generera en aktiveringsnyckel.

3. När du har fått aktiveringsnyckeln kan ISV-installationen lagra nyckeln lokalt på varje nod (till exempel i registret).

4. När du kör ett paket som använder den ISV licensierade komponent på en nod i Azure-SSIS-IR kunder paketet läser lokalt lagrade aktiveringsnyckeln och validerar den mot nodens kluster-ID. Paketet kan också rapportera Nodanatalet kluster med ISV-Aktiveringsservern.

    Här är ett exempel på kod som validerar aktiveringsnyckeln och rapporterar antalet för kluster-noden:

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

## <a name="next-steps"></a>Nästa steg

-   [Anpassad installation för Azure-SSIS-integrering runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise-versionen av körningsmiljön Azure SSIS-integrering](how-to-configure-azure-ssis-ir-enterprise-edition.md)