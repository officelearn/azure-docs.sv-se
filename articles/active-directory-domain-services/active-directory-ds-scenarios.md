---
title: 'Azure Active Directory Domain Services: Distributionsscenarier | Microsoft Docs'
description: Distributionsscenarier för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: db2bd855300d93d832a3dd7ca0ce526478824ccc
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502577"
---
# <a name="deployment-scenarios-and-use-cases"></a>Distributionsscenarier och användningsfall
I det här avsnittet ska titta vi på några scenarier och användningsfall som har nytta av Azure Active Directory (AD) Domain Services.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Säker och enkel administration av Azure-datorer
Du kan använda Azure Active Directory Domain Services för att hantera dina Azure-datorer i ett förenklat sätt. Azure-datorer kan kopplas till den hanterade domänen, vilket gör att du kan använda företagets AD autentiseringsuppgifter för inloggning. Den här metoden hjälper till att undvika credential management problem, till exempel underhålla lokala administratörskonton på alla virtuella datorer i Azure.

Server-datorer som är anslutna till den hanterade domänen kan också hanteras och skyddas med hjälp av en Grupprincip. Du kan använda den begärda baslinjer för virtuella datorer i Azure och låsa dem i enlighet med företagets riktlinjer. Du kan till exempel använda funktioner för hantering av grupp-princip för att begränsa vilka typer av program som kan startas på de virtuella datorerna.

![Effektiv administration av Azure-datorer](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Som servrar och annan infrastruktur når slutet på sin livscykel, flyttar Contoso många program som för närvarande finns lokalt och i molnet. Deras aktuella IT-standard mandat att servrar som är värd för företagets program måste vara ansluten till domänen och hanterade med hjälp av en Grupprincip. Contosos IT-administratören föredrar att domänen till virtuella datorer som distribueras i Azure, för att underlätta administration. Därför kan kan administratörer och användare logga in med sina företagsuppgifter. Datorer kan konfigureras för att uppfylla nödvändiga säkerheten med hjälp av en Grupprincip på samma gång. Contoso vill inte behöva distribuera, övervaka och hantera domänkontrollanter i Azure för att skydda Azure-datorer. Azure AD Domain Services är Därför passade bra för det här användningsfallet.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Hanterade domäner som tillhandahålls av Azure AD Domain Services ger en enkel fast (organisationsenhet) organisationsenhetsstruktur som standard. Alla domänanslutna datorer finns i en enstaka fast Organisationsenhet. Du kan dock välja att skapa anpassade organisationsenheter.
* Azure AD Domain Services har stöd för enkel Grupprincip i form av en inbyggda Grupprincipobjektet varje för användare och datorer behållare. Du kan skapa anpassade grupprincipobjekt och rikta dem till anpassade organisationsenheter.
* Azure AD Domain Services har stöd för det grundläggande AD dator objektet schemat. Du kan inte utöka schemat för den datorobjekt.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Lift and shift ett lokalt program som använder LDAP-bindningsautentisering till Azure Infrastructure Services
![LDAP-bindning](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso har ett lokalt program som köpts från en oberoende Programvaruleverantör många år sedan. Programmet för närvarande är i underhållsläge av Programvaruutvecklaren och begär ändringar i programmet är oöverkomligt dyra för Contoso. Det här programmet har en webbaserad klientdel som samlar in autentiseringsuppgifter för användare via ett webbformulär och autentiserar användare genom att utföra en LDAP-bindning till företagets Active Directory. Contoso vill flytta över det här programmet till Azure Infrastructure Services. Det är önskvärt att programmet fungerar som är, utan några ändringar. Dessutom kan användare ska kunna autentiseras med hjälp av sina befintliga inloggningsuppgifter och utan att behöva omtrimning av användare för att göra saker på olika sätt. Med andra ord slutanvändare bör vara oblivious av där programmet körs och migreringen ska vara transparenta till dem.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Se till att programmet inte behöver ändra/skrivning till katalogen. LDAP-skrivåtkomst till hanterade domäner som tillhandahålls av Azure AD Domain Services stöds inte.
* Du kan inte ändra lösenord direkt mot den hanterade domänen. Användarna kan ändra sina lösenord antingen med hjälp av Azure Active Directorys lösenord för självbetjäning ändringen mekanism eller mot en lokal katalog. Dessa ändringar är automatiskt synkroniserats och finns tillgängliga i den hanterade domänen.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Lift and shift ett lokalt program som använder LDAP läsa för att gå till katalogen till Azure Infrastructure Services
Contoso har ett lokalt line-of-business (LOB) program som har utvecklats specifikt nästan ett årtionde sedan. Det här programmet är directory medveten och har utformats för att arbeta med Windows Server AD. Programmet använder LDAP (Lightweight Directory Access Protocol) för att läsa information/attribut om användare från Active Directory. Programmet inte ändra attribut och annars skriva till katalogen. Contoso vill migrera det här programmet till Azure Infrastructure Services och dra tillbaka föråldras lokal maskinvara som för närvarande som är värd för det här programmet. Programmet kan inte skrivas över om du vill använda moderna directory API: er som REST-baserad Azure AD Graph API. Ett lift and shift-alternativ är därför det önskade innebär att programmet kan migreras för att köras i molnet, utan att ändra koden eller skriva om programmet.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Se till att programmet inte behöver ändra/skrivning till katalogen. LDAP-skrivåtkomst till hanterade domäner som tillhandahålls av Azure AD Domain Services stöds inte.
* Se till att programmet inte behöver en anpassad/utökat Active Directory-schemat. Schematillägg stöds inte i Azure AD Domain Services.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrera en den lokala tjänsten eller daemon program till Azure Infrastructure Services
Vissa program består av flera nivåer, där en av nivåerna behöver utföra autentiserade anrop till en backend-nivå, till exempel en databasnivå. Active Directory-tjänstkonton används ofta för dessa användningsområden. Du kan lift-and-shift sådana program på Azure Infrastructure Services och använda Azure AD Domain Services för identitet behov av dessa program. Du kan välja att använda samma konto som ska synkroniseras från din lokala katalog till Azure AD. Alternativt kan du först skapa en anpassad Organisationsenhet och sedan skapa ett separat tjänstkonto i denna Organisationsenhet för att distribuera sådana program.

![Tjänstkonto som använder WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso har skapats specifikt vault programvara som innehåller en frontwebb, en SQLServer och backend FTP-servern. Windows-integrerad autentisering av tjänstkonton används för att autentisera klientdelen till FTP-servern. Klientdelen har ställts in att köras som ett tjänstkonto. Backend-servern är konfigurerad för att godkänna åtkomst från kontot för klientdelen. Contoso föredrar att inte ska distribuera en domain controller virtuell dator i molnet för att flytta programmet till Azure Infrastructure Services. Contosos IT-administratören kan distribuera de servrar som är värd för klientdelen, SQLServer och FTP-servern till Azure-datorer. Dessa datorer kopplas sedan till en Azure AD Domain Services-hanterad domän. De kan sedan använda samma tjänstkonto i sina lokala katalog för appens autentisering. Det här tjänstkontot synkroniseras till den hanterade domänen i Azure AD Domain Services och är tillgänglig för användning.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Kontrollera att programmet använder användarnamn/lösenord för autentisering. Certifikat/smartkort-baserad autentisering stöds inte av Azure AD Domain Services.
* Du kan inte ändra lösenord direkt mot den hanterade domänen. Användarna kan ändra sina lösenord antingen med hjälp av Azure Active Directorys lösenord för självbetjäning ändringen mekanism eller mot en lokal katalog. Dessa ändringar är automatiskt synkroniserats och finns tillgängliga i den hanterade domänen.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server Remote desktop services-distributioner i Azure
Du kan använda Azure AD Domain Services för att förse din fjärrskrivbordsservrar som distribueras i Azure hanterade AD DS.

Läs mer om det här distributionsscenariot, hur du [integrera Azure AD Domain Services med RDS-distribution](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Domänanslutna HDInsight-kluster (förhandsversion)
Du kan ställa in ett Azure HDInsight-kluster som är ansluten till en Azure AD Domain Services-hanterad domän med Apache Ranger aktiverat. Skapa och tillämpa principer för Hive via Apache Ranger och ge användare (till exempel dataexperter) för att ansluta till Hive med hjälp av ODBC-baserade verktyg, till exempel Excel, bland annat Tableau osv. Microsoft arbetar på att lägga till andra arbetsbelastningar, till exempel HBase, Spark- och Storm, i domänanslutna HDInsight snart.

Läs mer om det här distributionsscenariot, hur du [konfigurera domänanslutna HDInsight-kluster](../hdinsight/domain-joined/apache-domain-joined-configure.md)
