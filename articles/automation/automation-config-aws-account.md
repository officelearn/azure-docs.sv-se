---
title: Autentisera Azure Automation-runbooks med Amazon Web Services
description: Den här artikeln beskriver hur du skapar och validerar AWS-autentiseringsuppgifter för runbooks i Azure Automation som hanterar AWS-resurser.
keywords: aws-autentisering, konfigurera aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 92919d2e0cc7ca685d2b60a8e7a8cf20433bbefc
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994701"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Autentisera Azure Automation-runbooks med Amazon Web Services

Du kan automatisera vanliga uppgifter med resurser i Amazon Web Services (AWS) med hjälp av Automation-runbooks i Azure. Du kan automatisera många aktiviteter i AWS med hjälp av Automation-runbooks precis som du kan göra med resurser i Azure. Du behöver bara två saker:

* En AWS-prenumeration och en uppsättning autentiseringsuppgifter. Mer specifikt din åtkomstnyckel  och hemliga nyckel för AWS. Mer information finns i artikeln [Använda AWS-autentiseringsuppgifter](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* En Azure-prenumeration och ett Automation-konto.

För att autentisera med AWS måste du ange en uppsättning AWS-autentiseringsuppgifter för autentisering av runbooks som körs från Azure Automation. Om du redan har skapat ett Automation-konto och du vill använda det för att autentisera med AWS kan du följa stegen i avsnittet nedan. Om du vill tilldela ett konto för Runbooks som riktar sig till AWS-resurser bör du först skapa ett nytt [Automation-konto](automation-create-standalone-account.md) och hoppa över steget för att skapa ett Kör som-konto. När du har skapat kontot följer du stegen nedan för att slutföra konfigurationen.

## <a name="configure-automation-account"></a>Konfigurera ett Automation-konto

För att Azure Automation ska kunna kommunicera med AWS måste du först hämta dina AWS-autentiseringsuppgifter och lagra dem som tillgångar i Azure Automation. Utför följande steg som finns dokumenterade i AWS-dokumentet [Hantera åtkomstnycklar för ditt AWS-konto](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) om du behöver skapa en åtkomstnyckel och kopiera sedan **åtkomstnyckelns ID** och **den hemliga åtkomstnyckeln** (om du vill kan du hämta nyckelfilen och lagra den på en säker plats).

När du har skapat och kopierat AWS-säkerhetsnycklarna måste du skapa en autentiseringstillgång med ett Azure Automation-konto för att lagra dem på ett säkert sätt och referera till dem med dina runbooks. Följ stegen i avsnittet: **om du vill skapa en ny autentiseringsuppgift** i [inloggnings resurserna i Azure Automation](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) artikel och ange följande information:

1. I rutan **Namn** anger du **AWScred** eller lämpligt värde som följer din namngivningskonvention.
2. I rutan **användar namn** anger du ditt **åtkomst-ID** och din **hemliga åtkomst nyckel** i rutan **lösen ord** och **Bekräfta lösen ord** .

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [automatiserar distributionen av en virtuell dator i Amazon Web Services](automation-scenario-aws-deployment.md) och lär dig hur du skapar runbooks för att automatisera uppgifter i AWS.
