---
title: Vanliga distributionsscenarier för Azure AD Domain Services | Microsoft-dokument
description: Lär dig mer om några av de vanliga scenarierna och användningsfallen för Azure Active Directory Domain Services för att ge värde och uppfylla affärsbehov.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917238"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Vanliga användningsfall och scenarier för Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP (Lightweight Directory Access Protocol) och Kerberos/NTLM-autentisering. Azure AD DS integreras med din befintliga Azure AD-klientorganisation, vilket gör det möjligt för användare att logga in med sina befintliga autentiseringsuppgifter. Du använder dessa domäntjänster utan att behöva distribuera, hantera och korrigera domänkontrollanter i molnet, vilket ger en smidigare lyft-och-förskjutning av lokala resurser till Azure.

I den här artikeln beskrivs några vanliga affärsscenarier där Azure AD DS ger värde och uppfyller dessa behov.

## <a name="secure-administration-of-azure-virtual-machines"></a>Säker administration av virtuella Azure-datorer

Azure-virtuella datorer (VMs) kan anslutas till en Azure AD DS-hanterad domän för att du ska kunna använda en enda uppsättning AD-autentiseringsuppgifter. Den här metoden minskar problem med hantering av autentiseringsuppgifter, till exempel att underhålla lokala administratörskonton på varje virtuell dator eller separata konton och lösenord mellan miljöer.

Virtuella datorer som är anslutna till en Azure AD DS-hanterad domän kan också hanteras och skyddas med hjälp av grupprinciper. Nödvändiga säkerhetsbaslinjer kan tillämpas på virtuella datorer för att låsa dem i enlighet med företagets säkerhetsriktlinjer. Du kan till exempel använda funktioner för grupprinciphantering för att begränsa de typer av program som kan startas på den virtuella datorn.

![Strömlinjeformad administration av virtuella Azure-datorer](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Låt oss titta på ett gemensamt exempel scenario. När servrar och annan infrastruktur når uttjänt vill Contoso flytta program som för närvarande finns lokalt till molnet. Deras nuvarande IT-standard mandat att servrar som är värdar för företagsprogram måste domän-anslutna och hanteras med hjälp av grupprincip. Contosos IT-administratör föredrar att domän ansluta till virtuella datorer som distribueras i Azure för att göra administrationen enklare eftersom användare sedan kan logga in med sina företagsautentiseringsuppgifter. När domänanslutna, virtuella datorer kan också konfigureras för att följa nödvändiga säkerhetsbaslinjer med hjälp av grupprincipobjekt (GPOs). Contoso föredrar att inte distribuera, övervaka och hantera sina egna domänkontrollanter i Azure.

Azure AD DS passar bra för det här användningsfallet. Med en Azure AD DS-hanterad domän kan du domän ansluta till virtuella datorer, använda en enda uppsättning autentiseringsuppgifter och tillämpa grupprinciper. Som en hanterad domän behöver du inte konfigurera och underhålla domänkontrollanterna själv.

### <a name="deployment-notes"></a>Distributionsanteckningar

Följande distributionsöverväganden gäller för det här exempelanvändningsfallet:

* Azure AD DS-hanterade domäner använder som standard en enda, platt organisationsenhetsstruktur. Alla domänanslutna virtuella datorer finns i en enda organisationsenhet. Om så önskas kan du skapa anpassade företagsenheter.
* Azure AD DS använder ett inbyggt GPO vardera för användare och datorbehållare. För ytterligare kontroll kan du skapa anpassade gpu:er och rikta dem till anpassade företagsenheter.
* Azure AD DS stöder det grundläggande AD-datorobjektschemat. Du kan inte utöka datorobjektets schema.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Lokalt lyft-och-skift-program som använder LDAP-bindningsautentisering

Som ett exempel scenario har Contoso ett lokalt program som köptes från en ISV för många år sedan. Programmet är för närvarande i underhållsläge av ISV och begär ändringar i programmet är oöverkomligt dyrt. Det här programmet har en webbaserad klientdel som samlar in användarautentiseringsuppgifter med hjälp av ett webbformulär och sedan autentiserar användare genom att utföra en LDAP-bindning till den lokala AD DS-miljön.

![LDAP-bindning](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso vill migrera det här programmet till Azure. Programmet bör fortsätta att fungera som det ska, utan att några ändringar behövs. Dessutom bör användare kunna autentisera med hjälp av sina befintliga företagsreferenser och utan ytterligare utbildning. Den bör vara transparent för slutanvändare där programmet körs.

I det här scenariot låter Azure AD DS program utföra LDAP-bindningar som en del av autentiseringsprocessen. Äldre lokala program kan lyfta och flytta till Azure och fortsätta att sömlöst autentisera användare utan att ändra konfigurationen eller användarupplevelsen.

### <a name="deployment-notes"></a>Distributionsanteckningar

Följande distributionsöverväganden gäller för det här exempelanvändningsfallet:

* Kontrollera att programmet inte behöver ändra/skriva till katalogen. LDAP-skrivåtkomst till en Azure AD DS-hanterad domän stöds inte.
* Du kan inte ändra lösenord direkt mot en Azure AD DS-hanterad domän. Slutanvändare kan ändra sitt lösenord antingen med hjälp av Azure AD:s mekanism för lösenordsändring av självbetjäning eller mot den lokala katalogen. Dessa ändringar synkroniseras och är sedan automatiskt synkroniserade och tillgängliga i azure AD DS-hanterade domänen.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Lift-and-shift lokala program som använder LDAP läsa för att komma åt katalogen

Precis som föregående exempelscenario, låt oss anta contoso har en lokal line-of-business (LOB) program som utvecklades nästan ett decennium sedan. Det här programmet är katalogmedveten och har utformats för att använda LDAP för att läsa information/attribut om användare från AD DS. Programmet ändrar inte attribut eller på annat sätt skriva till katalogen.

Contoso vill migrera det här programmet till Azure och dra tillbaka den åldrande lokala maskinvaran som för närvarande är värd för det här programmet. Programmet kan inte skrivas om för att använda moderna katalog-API:er, till exempel REST-baserade Microsoft Graph API. Ett lyft-och-skift-alternativ önskas där programmet kan migreras för att köras i molnet, utan att ändra kod eller skriva om programmet.

För att hjälpa till med det här scenariot låter Azure AD DS program utföra LDAP-läsningar mot den hanterade domänen för att få den attributinformation som behövs. Programmet behöver inte skrivas om, så en hiss-och-flytta till Azure kan användare fortsätta att använda appen utan att inse att det finns en förändring i var den körs.

### <a name="deployment-notes"></a>Distributionsanteckningar

Följande distributionsöverväganden gäller för det här exempelanvändningsfallet:

* Kontrollera att programmet inte behöver ändra/skriva till katalogen. LDAP-skrivåtkomst till en Azure AD DS-hanterad domän stöds inte.
* Kontrollera att programmet inte behöver ett anpassat/utökat Active Directory-schema. Schematillägg stöds inte i Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrera ett lokalt tjänst- eller demonprogram till Azure

Vissa program innehåller flera nivåer, där en av nivåerna måste utföra autentiserade anrop till en backend-nivå, till exempel en databas. AD-tjänstkonton används ofta i dessa scenarier. När du lyfter och flyttar program till Azure kan du med Azure AD DS fortsätta att använda tjänstkonton på samma sätt. Du kan välja att använda samma tjänstkonto som synkroniseras från din lokala katalog till Azure AD eller skapa en anpassad organisationsenhet och sedan skapa ett separat tjänstkonto i den organisationsenheten. Med båda tillvägagångssätten fortsätter programmen att fungera på samma sätt för att ringa autentiserade samtal till andra nivåer och tjänster.

![Tjänstkonto med WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

I det här exemplet har Contoso ett specialbyggt program för programvaruvalv som innehåller en webbklämmning, en SQL-server och en serverdels FTP-server. Windows-integrerad autentisering med tjänstkonton autentiserar webbfronten till FTP-servern. Webbfronten är inställd på att köras som ett tjänstkonto. Serverdelsservern är konfigurerad för att auktorisera åtkomst från tjänstkontot för webbklämningen. Contoso vill inte distribuera och hantera egna virtuella domänkontrollanter i molnet för att flytta det här programmet till Azure.

I det här scenariot kan servrarna som är värdar för webbklämman, SQL-servern och FTP-servern migreras till virtuella Azure-datorer och anslutas till en Azure AD DS-hanterad domän. De virtuella datorerna kan sedan använda samma tjänstkonto i sin lokala katalog för appens autentiseringsändamål, som synkroniseras via Azure AD med Azure AD Connect.

### <a name="deployment-notes"></a>Distributionsanteckningar

Följande distributionsöverväganden gäller för det här exempelanvändningsfallet:

* Se till att programmen använder ett användarnamn och lösenord för autentisering. Certifikat- eller smartcardbaserad autentisering stöds inte av Azure AD DS.
* Du kan inte ändra lösenord direkt mot en Azure AD DS-hanterad domän. Slutanvändare kan ändra sitt lösenord antingen med hjälp av Azure AD:s mekanism för lösenordsändring av självbetjäning eller mot den lokala katalogen. Dessa ändringar synkroniseras och är sedan automatiskt synkroniserade och tillgängliga i azure AD DS-hanterade domänen.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Distributioner av fjärrskrivbordstjänster för Windows Server i Azure

Du kan använda Azure AD DS för att tillhandahålla hanterade domäntjänster till fjärrskrivbordsservrar som distribueras i Azure. Mer information om det här distributionsscenariot finns i [hur du integrerar Azure AD Domain Services med distributionen av FJÄRRSKRIVBORDS][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Domänanstit anslutna HDInsight-kluster

Du kan konfigurera ett Azure HDInsight-kluster som är anslutet till en Azure AD DS-hanterad domän med Apache Ranger aktiverat. Du kan skapa och tillämpa Hive-principer via Apache Ranger och tillåta användare, till exempel datavetare, att ansluta till Hive med ODBC-baserade verktyg som Excel eller Tableau. Vi fortsätter att arbeta för att lägga till andra arbetsbelastningar, till exempel HBase, Spark och Storm till domänanslutna HDInsight.

Mer information om det här distributionsscenariot finns i [konfigurera domänanslutna HDInsight-kluster][hdinsight]

## <a name="next-steps"></a>Nästa steg

Skapa och [konfigurera en Azure Active Directory Domain Services-instans][tutorial-create-instance] för att komma igång

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
