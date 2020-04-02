---
title: Konfigurera autentisering med Amazon Web Services
description: Den här artikeln beskriver hur du skapar och validerar AWS-autentiseringsuppgifter för runbooks i Azure Automation som hanterar AWS-resurser.
keywords: aws-autentisering, konfigurera aws
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 891db5d17ddde681f75845fdca6c9252c5b32b86
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520819"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autentisera runbooks med Amazon Web Services (AWS)

Du kan automatisera vanliga uppgifter med resurser i Amazon Web Services (AWS) med hjälp av Automation-runbooks i Azure. Du kan automatisera många aktiviteter i AWS med hjälp av Automation-runbooks precis som du kan göra med resurser i Azure. Du behöver bara två saker:

* En AWS-prenumeration och en uppsättning autentiseringsuppgifter. Mer specifikt din åtkomstnyckel  och hemliga nyckel för AWS. Mer information finns i artikeln [Använda AWS-autentiseringsuppgifter](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* En Azure-prenumeration och ett Automation-konto.

För att autentisera med AWS måste du ange en uppsättning AWS-autentiseringsuppgifter för autentisering av runbooks som körs från Azure Automation. Om du redan har skapat ett Automation-konto och vill använda det för att autentisera med AWS kan du följa stegen i följande avsnitt: Om du vill dedikera ett konto för runbooks som riktar dig till AWS-resurser bör du först skapa ett nytt [Automation-konto](automation-offering-get-started.md) (hoppa över alternativet för att skapa ett huvudnamn för tjänsten) och använda följande steg:

## <a name="configure-automation-account"></a>Konfigurera ett Automation-konto

För att Azure Automation ska kunna kommunicera med AWS måste du först hämta dina AWS-autentiseringsuppgifter och lagra dem som tillgångar i Azure Automation. Utför följande steg som finns dokumenterade i AWS-dokumentet [Hantera åtkomstnycklar för ditt AWS-konto](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) om du behöver skapa en åtkomstnyckel och kopiera sedan **åtkomstnyckelns ID** och **den hemliga åtkomstnyckeln** (om du vill kan du hämta nyckelfilen och lagra den på en säker plats).

När du har skapat och kopierat AWS-säkerhetsnycklarna måste du skapa en autentiseringstillgång med ett Azure Automation-konto för att lagra dem på ett säkert sätt och referera till dem med dina runbooks. Följ stegen i avsnittet: **Så här skapar du en ny autentiseringsartikel** i [autentiseringsuppgifterna i](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) Azure Automation och anger följande information:

1. I rutan **Namn** anger du **AWScred** eller lämpligt värde som följer din namngivningskonvention.
2. Skriv ditt **Åtkomst-ID** och din **hemliga åtkomstnyckel** i rutan **Lösenord** och **Bekräfta lösenord** i rutan **Användarnamn.**

## <a name="next-steps"></a>Nästa steg

* Läs artikeln [Automatisera distributionen av en virtuell dator i Amazon Web Services](automation-scenario-aws-deployment.md) om du vill veta mer om hur du skapar runbooks för att automatisera uppgifter i AWS.
