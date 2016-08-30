<properties
   pageTitle="Säkerheten i Azure Automation"
   description="Den här artikeln innehåller en översikt över automatiseringssäkerheten och de olika tillgängliga autentiseringsmetoderna för Automation-konton i Azure Automation."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="automation security, secure automation" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte" />

# Säkerheten i Azure Automation
Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS).  För att en runbook ska kunna utföra sina åtgärder måste den ha behörighet att komma åt resurserna på ett säkert sätt med den minsta behörighet som krävs i prenumerationen.  
Den här artikeln täcker de olika autentiseringsscenarier som stöds av Azure Automation och visar hur du kommer igång baserat på den eller de miljöer som du behöver hantera.  

## Översikt över Automation-konton
När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser (runbooks, tillgångar, konfigurationer) från resurserna som finns i andra Automation-konton. Du kan använda Automation-konton för att hålla resurser åtskilda i separata logiska miljöer. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö.  Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen.

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men Automation-konton kan hantera resurser i alla regioner. Huvudskälet till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en specifik region.

>[AZURE.NOTE]Automation-konton, och de resurser som de innehåller, som skapats på Azure-portalen kan inte nås på den klassiska Azure-portalen. Om du vill hantera dessa konton eller deras resurser med Windows PowerShell måste du använda Azure Resource Manager-modulerna.

Alla aktiviteter som du utför mot resurser med hjälp av Azure Resource Manager (ARM) och Azure-cmdlets i Azure Automation måste autentisera till Azure med hjälp av autentiseringsuppgiftsbaserad autentisering med organisationens Azure Active Directory-ID.  Certifikatbaserad autentisering var den ursprungliga autentiseringsmetoden i Azure Service Management-läge (ASM), men det var svårt att konfigurera.  Autentisering i Azure med Azure AD-användare infördes igen 2014 inte bara för att förenkla processen att konfigurera ett autentiseringskonto, men även för att ge stöd för icke-interaktiv autentisering i Azure med ett enda användarkonto som fungerade med både ASM- och ARM-läge.   

Vi släppte nyligen en till uppdatering, där ett Azure AD-tjänstobjekt skapas automatiskt när Automation-kontot har skapats. Detta kallas för ett ”Kör som”-konto i Azure och är standardautentiseringsmetoden för runbook-automatisering med Azure Resource Manager.     

Rollbaserad åtkomstkontroll är tillgängligt i ARM-läge för att tillåta tillåtna åtgärder mot ett Azure AD-användarkonto och tjänstobjekt och för att autentisera tjänstobjektet.  Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](../automation/automation-role-based-access-control.md).  

Runbooks som körs i en Hybrid Runbook Worker i ditt datacenter eller mot beräkningstjänster i AWS kan inte använda samma metod som normalt används för runbooks-autentisering mot Azure-resurser.  Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt.  

## Autentiseringsmetoder

Följande tabell sammanfattar de olika autentiseringsmetoderna för varje miljö som stöds av Azure Automation och artikeln som beskriver hur du konfigurerar autentisering för dina runbooks.

Metod  |  Miljö  | Artikel
----------|----------|----------
Azure AD-användarkonto | Azure Resource Manager och Azure Service Management | [Autentisera Runbooks med Azure AD-användarkonto](../automation/automation-sec-configure-aduser-account.md)
Azure AD-tjänstobjekt | Azure Resource Manager | [Autentisera runbooks med ett ”Kör som”-konto i Azure](../automation/automation-sec-configure-azure-runas-account.md)
Windows-autentisering | Lokalt datacenter | [Autentisera runbooks för Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md)
AWS-autentiseringsuppgifter | Amazon Web Services | [Autentisera runbooks med Amazon Web Services (AWS)](../automation/automation-sec-configure-aws-account.md)



<!--HONumber=jun16_HO2-->


