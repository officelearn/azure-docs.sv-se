---
title: Installera licensierade komponenter för Azure-SSIS integration runtime | Microsoft Docs
description: Lär dig hur du gör en ISV kan du utveckla och installera betald eller licensierad anpassade komponenter för Azure-SSIS integration runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022333"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Installera betald eller licensierad anpassade komponenter för Azure-SSIS integration runtime

Den här artikeln beskrivs hur en ISV kan utveckla och installera betald eller licensierad anpassade komponenter för SQL Server Integration Services (SSIS)-paket som körs i Azure i Azure-SSIS integration runtime.

## <a name="the-problem"></a>Problemet

Typen av Azure-SSIS integration runtime medför flera utmaningar, vilket gör de vanliga licensiering metoder som används för den lokala installationen av anpassade komponenter som saknas. Därför kan kräver Azure-SSIS IR en annan metod.

-   Noder i Azure-SSIS IR är föränderliga och kan allokeras eller när som helst. Du kan till exempel starta eller stoppa noder hantera kostnaden eller skala upp och ned via olika nodstorlekar. Därför är Binder en tredjepartskomponent-licens till en viss nod med hjälp av datorspecifik information, till exempel MAC-adress eller CPU-ID inte längre lönsamma.

-   Du kan även skala Azure-SSIS IR in eller ut, så att antalet noder kan krympa eller utöka när som helst.

## <a name="the-solution"></a>Lösningen

På grund av begränsningar av traditionella licensiering metoder som beskrivs i föregående avsnitt, är Azure-SSIS IR en ny lösning. Den här lösningen använder Windows-miljövariabler och SSIS systemvariabler för licens bindnings- och verifiering av komponenter från tredje part. ISV: er kan använda dessa variabler för att hämta information om unika och beständiga för en Azure-SSIS IR, t.ex kluster-ID och antalet klusternoder. Med den här informationen kan ISV: er sedan binda licensen för sina komponent till en Azure-SSIS IR *som ett kluster*. Den här bindningen använder ett ID som inte ändras när kunder starta eller stoppa, skala upp eller ned skala in eller ut eller konfigurera om Azure-SSIS IR på något sätt.

Följande diagram visar standardinstallation, aktivering och vazba licence och verifiering flöden för komponenter från tredje part som använder dessa nya variabler:

![Installation av licensierade komponenter](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruktioner
1. ISV: er kan erbjuda sina licensierade komponenter i olika SKU: er eller nivåer (till exempel nod, upp till 5 noder, upp till 10 noder och så vidare). ISV: er ger motsvarande produktnyckeln när kunderna köper en produkt. ISV: er kan också ge en Azure Storage blob-behållare som innehåller en ISV installationsskriptet och tillhörande filer. Kunder kan kopiera filerna till sina egna lagringsbehållare och ändra dem med sina egna produktnyckel (till exempel genom att köra `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Kunder kan sedan etablera eller konfigurera om Azure-SSIS IR med SAS-URI för sina behållare som parameter. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md).

2. När Azure-SSIS IR är etablerad eller omkonfigureras, ISV-installationen körs på varje nod för att söka i Windows-miljövariablerna `SSIS_CLUSTERID` och `SSIS_CLUSTERNODECOUNT`. Azure-SSIS IR skickar sedan dess kluster-ID och produktnyckeln för den licensierade produkten med ISV-Aktiveringsservern för att generera en nyckel för aktivering.

3. När du har fått aktiveringsnyckeln kan ISV-installationen lagra nyckeln lokalt på varje nod (till exempel i registret).

4. När kunder kör ett paket som använder den ISV licensierade komponent på en nod i Azure-SSIS IR måste paketet läser lokalt lagrade aktiveringsnyckeln och validerar den mot nodens kluster-ID. Paketet kan också välja att rapportera antalet klusternoder med ISV-Aktiveringsservern.

    Här är ett exempel på kod som verifierar aktiveringsnyckeln och rapporterar antalet klusternoder:

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

## <a name="isv-partners"></a>ISV-partners

Du hittar en lista med ISV-partners som har anpassat deras komponenter och tillägg för Azure-SSIS IR i slutet av det här blogginlägget - [Enterprise Edition, anpassad installation och 3 part utökningsbarhet för SSIS i ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>Nästa steg

-   [Anpassad installation för den Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise-versionen av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
