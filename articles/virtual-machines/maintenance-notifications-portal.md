---
title: Använda portalen för underhållsmeddelanden
description: Visa underhållsmeddelanden för virtuella datorer som körs i Azure och starta självbetjäningsunderhåll med hjälp av portalen.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115737"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Hantera planerade underhållsmeddelanden med hjälp av portalen

**Den här artikeln gäller virtuella datorer som kör både Linux och Windows.**

När en [planerad underhållsvåg](maintenance-notifications.md) har schemalagts kan du söka efter en lista över virtuella datorer som påverkas. 

Du kan använda Azure-portalen och leta efter virtuella datorer som schemalagts för underhåll.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Klicka på Virtuella datorer i den vänstra **navigeringen.**

3. I fönstret Virtuella datorer väljer du knappen **Redigera kolumner** för att öppna listan över tillgängliga kolumner.

4. Markera och lägg till följande kolumner:

   **Underhållsstatus**: Visar underhållsstatus för den virtuella datorn. Följande är de potentiella värdena:
      
      | Värde | Beskrivning |
      |-------|-------------|
      | Starta nu | Den virtuella datorn finns i självbetjäningsunderhållsfönstret som låter dig starta underhållet själv. Se nedan om hur du startar underhåll på din virtuella dator. | 
      | Schemalagd | Den virtuella datorn är schemalagd för underhåll utan något alternativ för dig att starta underhållet. Du kan lära dig om underhållsfönstret genom att välja fönstret Underhåll - Schemalagt i den här vyn eller genom att klicka på den virtuella datorn. | 
      | Redan uppdaterad | Den virtuella datorn har redan uppdaterats och inga ytterligare åtgärder krävs just nu. | 
      | Försök igen senare | Du har initierat underhåll utan framgång. Du kommer att kunna använda underhållsalternativet för självbetjäning vid ett senare tillfälle. | 
      | Försök igen nu | Du kan försöka igen med ett tidigare misslyckat självinitierat underhåll. | 
      | - | Den virtuella datorn ingår inte i en planerad underhållsvåg. |
      

   **Underhåll - Självbetjäningsfönster**: Visar tidsfönstret när du kan självstarta underhåll på dina virtuella datorer.
   
   **Underhåll - Schemalagt fönster**: Visar tidsfönstret när Azure kommer att underhålla din virtuella dator för att slutföra underhållet. 



## <a name="notification-and-alerts-in-the-portal"></a>Meddelanden och varningar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till prenumerationsägaren och ägargruppen. Du kan lägga till ytterligare mottagare och kanaler i den här kommunikationen genom att skapa Azure-aktivitetsloggaviseringar. Mer information finns i [Skapa aktivitetsloggaviseringar för tjänstmeddelanden](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

Se till att du anger **händelsetypen** som **planerat underhåll**och **tjänster** som **skaluppsättningar för virtuella datorer** och/eller **virtuella datorer**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Starta underhåll på den virtuella datorn från portalen

När du tittar på den virtuella datorn detaljer, kommer du att kunna se mer underhållsrelaterade detaljer.  
Högst upp i den virtuella datorns informationsvy läggs ett nytt meddelandemenyer till om den virtuella datorn ingår i en planerad underhållsvåg. Dessutom läggs ett nytt alternativ till för att starta underhållet när det är möjligt. 


Klicka på underhållsmeddelandet för att se underhållssidan med mer information om det planerade underhållet. Därifrån kommer du att kunna **börja underhåll** på din virtuella dator.

När du har påbörjat underhållet kommer den virtuella datorn att underhållas och underhållsstatusen uppdateras så att den återspeglar resultatet inom några minuter.

Om du missade självbetjäningsfönstret kan du fortfarande se fönstret när den virtuella datorn kommer att underhållas av Azure. 


## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med [Azure CLI](maintenance-notifications-cli.md) eller [PowerShell](maintenance-notifications-powershell.md).