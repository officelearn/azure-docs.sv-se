---
title: Konfigurera autentisering med Amazon Web Services | Microsoft Docs
description: "Den här artikeln beskriver hur du skapar och validerar AWS-autentiseringsuppgifter för runbooks i Azure Automation som hanterar AWS-resurser."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: aws-autentisering, konfigurera aws
ms.assetid: b6dde4bb-26ac-4876-9aa9-e586bed30d6b
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 00b217a4cddac0a893564db27ffb4f460973c246
ms.openlocfilehash: eee65672b3a9615afe2850b0cbc6daa275fc04ec


---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autentisera runbooks med Amazon Web Services (AWS)
Du kan automatisera vanliga uppgifter med resurser i Amazon Web Services (AWS) med hjälp av Automation-runbooks i Azure.  Du kan automatisera många aktiviteter i AWS med hjälp av Automation-runbooks precis som du kan göra med resurser i Azure.  Du behöver bara två saker:

* En AWS-prenumeration och en uppsättning autentiseringsuppgifter.  Mer specifikt din åtkomstnyckel  och hemliga nyckel för AWS.  Mer information finns i artikeln [Använda AWS-autentiseringsuppgifter](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* En Azure-prenumeration och ett Automation-konto.  Mer information om hur du konfigurerar ett Azure Automation-konto finns i artikeln [Konfigurera ett ”Kör som”-konto i Azure](automation-sec-configure-azure-runas-account.md).  

För att autentisera med AWS måste du ange en uppsättning AWS-autentiseringsuppgifter för autentisering av runbooks som körs från Azure Automation. Om du redan har skapat ett Automation-konto och du vill använda det för att autentisera med AWS kan du följa stegen i avsnittet nedan.  Om du vill använda ett särskilt konto för runbooks som körs mot AWS-resurser bör du först skapa ett nytt [”Kör som”-konto för Automation](automation-sec-configure-azure-runas-account.md) (hoppa över alternativet för att skapa ett tjänstobjekt) och sedan följa stegen nedan.

## <a name="configure-automation-account"></a>Konfigurera ett Automation-konto
För att Azure Automation ska kunna kommunicera med AWS måste du först hämta dina AWS-autentiseringsuppgifter och lagra dem som tillgångar i Azure Automation.  Utför följande steg som finns dokumenterade i AWS-dokumentet [Hantera åtkomstnycklar för ditt AWS-konto](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) om du behöver skapa en åtkomstnyckel och kopiera sedan **åtkomstnyckelns ID** och **den hemliga åtkomstnyckeln** (om du vill kan du hämta nyckelfilen och lagra den på en säker plats).

När du har skapat och kopierat AWS-säkerhetsnycklarna måste du skapa en autentiseringstillgång med ett Azure Automation-konto för att lagra dem på ett säkert sätt och referera till dem med dina runbooks.  Följ stegen i avsnittet **Skapa en ny autentiseringsuppgift** i artikeln [Autentiseringstillgångar i Azure Automation](automation-credentials.md#creating-a-new-credential-asset) och ange följande information:

1. I rutan **Namn** anger du **AWScred** eller lämpligt värde som följer din namngivningskonvention.  
2. Skriv ditt **åtkomst-ID** i rutan **Användarnamn** och din **hemliga åtkomstnyckel** i rutan **Lösenord** och **Bekräfta lösenord**.   

## <a name="next-steps"></a>Nästa steg
* Läs artikeln [Automatisera distributionen av en virtuell dator i Amazon Web Services](automation-scenario-aws-deployment.md) om du vill veta mer om hur du skapar runbooks för att automatisera uppgifter i AWS.



<!--HONumber=Nov16_HO2-->


