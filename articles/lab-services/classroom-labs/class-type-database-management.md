---
title: Konfigurera ett labb för att lära ut databashantering för relationsdatabaser | Microsoft-dokument
description: Lär dig hur du konfigurerar ett labb för att lära ut hanteringen av relationsdatabaser.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469926"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Konfigurera ett labb för att lära ut databashantering för relationsdatabaser

I den här artikeln beskrivs hur du konfigurerar ett labb för en grundläggande databashanteringsklass i Azure Lab Services. Databaskoncept är en av de introduktionskurser som undervisas vid de flesta datavetenskapliga avdelningar på college. SQL (Structured Query Language) är en internationell standard. SQL är standardspråket för relationsdatabashantering, inklusive att lägga till, komma åt och hantera innehåll i en databas.  Det är mest känt för sin snabba bearbetning, beprövad tillförlitlighet, lätthet och flexibilitet i användningen.

I den här artikeln visar vi hur du konfigurerar en mall för virtuella datorer i ett labb med både MySQL Database Server och SQL Server 2019-servern.  [MySQL](https://www.mysql.com/) är ett fritt tillgängligt relationsdatabashanteringssystem med öppen källkod (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) är den senaste versionen av Microsofts RDBMS.

## <a name="lab-configuration"></a>Labbkonfiguration

För att konfigurera det här labbet behöver du en Azure-prenumeration och labbkonto för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har fått en Azure-prenumeration kan du skapa ett nytt labbkonto i Azure Lab Services. Mer information om hur du skapar ett nytt labbkonto finns i [Självstudiekurs för att konfigurera ett labbkonto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labbkonto.

### <a name="lab-account-settings"></a>Inställningar för labbkonto

Aktivera inställningarna som beskrivs i tabellen nedan för labbkontot. Mer information om hur du aktiverar marketplace-avbildningar finns i [Ange Marketplace-avbildningar som är tillgängliga för labbskapare](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Inställning av labbkonto | Instruktioner |
| ------------------- | ------------ |
|Marketplace-bild| Aktivera avbildningen "SQL Server 2019 Standard på Windows Server 2019" för användning i ditt labbkonto.|

### <a name="lab-settings"></a>Labbinställningar

Använd inställningarna i tabellen nedan när du konfigurerar ett klassrumslabb.  Mer information om hur du skapar ett klassrumslabb finns i [konfigurera en självstudiekurs för klassrumslabb](tutorial-setup-classroom-lab.md).

| Labbinställningar | Värde/instruktioner |
| ------------ | ------------------ |
|Storlek på virtuell dator| Medel. Den här storleken passar bäst för relationsdatabaser, cachelagring i minnet och analyser.|
|Bild av virtuell dator| SQL Server 2019 Standard på Windows Server 2019|

## <a name="template-machine-configuration"></a>Konfiguration av mallmaskin

Om du vill installera MySQL på Windows Server 2019 kan du följa stegen i [Installera och kör MySQL Community Server på en virtuell dator](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 är förinstallerat i den virtuella datoravbildning vi valde när du skapade det nya labbet.

## <a name="cost-estimate"></a>Kostnadsuppskattning

Låt oss täcka en möjlig kostnadsuppskattning för den här klassen.  Vi använder en klass på 25 elever.  Det finns 20 timmars schemalagd lektionstid.  Dessutom får varje elev 10 timmars kvot för läxor eller uppgifter utanför schemalagd lektionstid.  Den virtuella maskinen storlek vi valde var medium, vilket är 42 lab enheter.

Här är ett exempel på en möjlig kostnadsuppskattning för den här klassen:

25 \* studenter (20 schemalagda timmar + \* 10 kvottimmar) 0,42 USD per timme = 315,00 USD

Mer information om prissättning finns i [Azure Lab Services Prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats

I den här artikeln gick du igenom de steg som krävs för att skapa ett labb för grundläggande databashanteringskoncept med både MySQL och SQL Server. Du kan använda en liknande inställning för andra databasklasser.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att konfigurera ett labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Länkar till e-postregistrering till studenter](how-to-configure-student-usage.md#send-invitations-to-users)
