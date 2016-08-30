<properties
   pageTitle="Konfigurera autentisering med Amazon Web Services | Microsoft Azure"
   description="Den här artikeln beskriver hur du skapar och validerar AWS-autentiseringsuppgifter för runbooks i Azure Automation som hanterar AWS-resurser."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="aws authentication, configure aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/10/2016"
   ms.author="magoedte"/>

# Autentisera runbooks med Amazon Web Services (AWS)
Du kan automatisera vanliga uppgifter med resurser i Amazon Web Services (AWS) med hjälp av Automation-runbooks i Azure.  Du kan automatisera många aktiviteter i AWS med hjälp av Automation-runbooks precis som du kan göra med resurser i Azure.  Du behöver bara två saker:

* En AWS-prenumeration och en uppsättning autentiseringsuppgifter.  Mer specifikt din åtkomstnyckel  och hemliga nyckel för AWS.  Mer information finns i artikeln [Använda AWS-autentiseringsuppgifter](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* En Azure-prenumeration och ett Automation-konto.  Mer information om hur du konfigurerar ett Azure Automation-konto finns i artikeln [Konfigurera ett ”Kör som”-konto i Azure](../automation/automation-sec-configure-azure-runas-account.md).  

För att autentisera med AWS måste du ange en uppsättning AWS-autentiseringsuppgifter för autentisering av runbooks som körs från Azure Automation. Om du redan har skapat ett Automation-konto och du vill använda det för att autentisera med AWS kan du följa stegen i avsnittet nedan.  Om du vill använda ett särskilt konto för runbooks som körs mot AWS-resurser bör du först skapa ett nytt [”Kör som”-konto för Automation](../automation/automation-sec-configure-azure-runas-account) (hoppa över alternativet för att skapa ett tjänstobjekt) och sedan följa stegen nedan.

## Konfigurera ett Automation-konto
För att Azure Automation ska kunna kommunicera med AWS måste du först hämta dina AWS-autentiseringsuppgifter och lagra dem som tillgångar i Azure Automation.  Utför följande steg som finns dokumenterade i AWS-dokumentet [Hantera åtkomstnycklar för ditt AWS-konto](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) om du behöver skapa en åtkomstnyckel och kopiera sedan **åtkomstnyckelns ID** och **den hemliga åtkomstnyckeln** (om du vill kan du hämta nyckelfilen och lagra den på en säker plats).

När du har skapat och kopierat AWS-säkerhetsnycklarna måste du skapa en autentiseringstillgång med ett Azure Automation-konto för att lagra dem på ett säkert sätt och referera till dem med dina runbooks.  Följ stegen i avsnittet **Så här skapar du en ny autentiseringsuppgift** i artikeln [Autentiseringstillgångar i Azure Automation](../automation/automation-certificates.md/###To create a new credential with the Azure portal) och ange följande information:

1. I rutan **Namn** anger du **AWScred** eller lämpligt värde som följer din namngivningskonvention.  
2. Skriv ditt **åtkomst-ID** i rutan **Användarnamn** och din **hemliga åtkomstnyckel** i rutan **Lösenord** och **Bekräfta lösenord**.   

## Nästa steg

- Läs artikeln [Automatisera distributionen av en virtuell dator i Amazon Web Services](../automation/automation-scenario-aws-deployment.md) om du vill veta mer om hur du skapar runbooks för att automatisera uppgifter i AWS.



<!--HONumber=jun16_HO2-->


