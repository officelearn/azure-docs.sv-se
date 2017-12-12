---
title: "Azure Active Directory Domain Services: Scenarier för distribution av | Microsoft Docs"
description: "Distributionsscenarier för Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: 11844fb8fabada9d863fe4adf0839ae6fa2ed101
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="deployment-scenarios-and-use-cases"></a>Distributionsscenarier och användningsfall
I det här avsnittet ska titta vi på några scenarier och användningsområden som är undantagna från Azure Active Directory (AD) Domain Services.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Säker, enkel administration av virtuella Azure-datorer
Du kan använda Azure Active Directory Domain Services för att hantera dina Azure virtuella datorer på ett effektivt sätt. Virtuella Azure-datorer kan anslutas till den hanterade domänen, vilket gör att du kan använda AD företagets inloggningsuppgifter för att logga in. Den här metoden hjälper till att undvika autentiseringsuppgifter management problem, till exempel underhålla lokala administratörskonton på var och en av dina virtuella Azure-datorer.

Server-datorer som är anslutna till den hanterade domänen kan också hanteras och skyddas med en Grupprincip. Du kan använda baslinjer krävs säkerhet för dina virtuella Azure-datorer och låsa dem i enlighet med företagets riktlinjer. Du kan till exempel använda funktioner för hantering av grupp-policy för att begränsa vilka typer av program som kan startas på dessa virtuella datorer.

![Effektiv administration av virtuella Azure-datorer](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Som når slutet av livslängd för servrar och annan infrastruktur, flyttar Contoso många program som finns lokalt till molnet. Deras aktuella IT-standarden kräver att servrarna som är värd för företagets program måste vara domänanslutna och hanterad med hjälp av en Grupprincip. Contosos IT-administratören föredrar att domänen ansluta till virtuella datorer i Azure, så att det blir enklare administration. Detta innebär att administratörer och användare kan logga in med sina företagsuppgifter. Datorer kan konfigureras för att uppfylla baslinjer för krävs säkerhet med hjälp av en Grupprincip på samma gång. Contoso föredrar att inte ska distribuera, övervaka och hantera domänkontrollanter i Azure för att skydda virtuella Azure-datorer. Därför Azure AD Domain Services är ett bra val för den här användningsfall.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Hanterade domäner som tillhandahålls av Azure AD Domain Services ger en enda flat OU (organisationsenhet)-struktur som standard. Alla domänanslutna datorer placeras i en enda flat Organisationsenhet. Du kan dock välja att skapa anpassade organisationsenheter.
* Azure AD Domain Services har stöd för enkla Grupprincip i form av ett inbyggda GPO varje för användare och datorer som behållare. Du kan skapa anpassade grupprincipobjekt och rikta dem till anpassade organisationsenheter.
* Azure AD Domain Services stöder grundläggande AD datorn objektet schemat. Du kan inte utöka schemat för det datorobjekt.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Lift och SKIFT ett lokalt program som använder LDAP bind autentisering till Azure Infrastructure Services
![LDAP-bindning](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso har ett lokalt program som köpts från en ISV många år sedan. Programmet är för närvarande i underhållsläge ISV: er och begär ändringar i programmet är orimligt hög för Contoso. Det här programmet har en webbaserad klientdel som samlar in autentiseringsuppgifter med hjälp av ett webbformulär och autentiserar användare genom att utföra en LDAP-bindning till företagets Active Directory. Contoso vill flytta programmet till Azure Infrastructure Services. Det är önskvärt att programmet fungerar som är utan ändringar. Dessutom kan användare ska kunna autentiseras med hjälp av sina befintliga företagsuppgifter och utan att behöva träna om användare för att göra saker på olika sätt. Med andra ord slutanvändare bör vara oblivious av där programmet körs och migreringen ska vara transparent för dem.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Se till att programmet inte behöver ändra/skrivning till katalogen. LDAP-skrivåtkomst till hanterade domäner som tillhandahålls av Azure AD Domain Services stöds inte.
* Det går inte att ändra lösenord direkt mot den hanterade domänen. Användare kan ändra sina lösenord antingen med hjälp av Azure AD självbetjäning lösenord ändra mekanism eller i förhållande till den lokala katalogen. Dessa ändringar är automatiskt synkroniserats och finns tillgängliga i den hanterade domänen.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Lift och SKIFT ett lokalt program som använder LDAP läsa för att få åtkomst till katalogen till Azure Infrastructure Services
Contoso har ett lokalt branschspecifika (LOB)-program som har utvecklats nästan tio år sedan. Det här programmet är directory medveten och har utformats för att fungera med Windows Server AD. Programmet använder LDAP (Lightweight Directory Access Protocol) för att läsa information/attribut om användare från Active Directory. Programmet inte ändra attribut eller på annat sätt skriva till katalogen. Contoso vill migrera programmet till Azure Infrastructure Services och dra tillbaka åldern lokal maskinvara som för närvarande är värd för det här programmet. Programmet kan inte skrivas över om du vill använda moderna directory API: er som REST-baserad Azure AD Graph API. Därför är ett alternativ för lift och SKIFT önskade innebär att programmet kan migreras om du vill köra i molnet, utan att ändra koden eller skriva om programmet.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Se till att programmet inte behöver ändra/skrivning till katalogen. LDAP-skrivåtkomst till hanterade domäner som tillhandahålls av Azure AD Domain Services stöds inte.
* Se till att programmet inte behöver en anpassad utökat Active Directory-schemat. Schematillägg stöds inte i Azure AD Domain Services.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrera en lokal tjänst eller daemon programmet till Azure Infrastructure Services
Vissa program består av flera nivåer där någon av nivåerna behöver utföra autentiserade anrop till en backend-nivå, till exempel en databasnivå. Active Directory-tjänstkonton används ofta för dessa användningsområden. Du kan lift och SKIFT sådana program på Azure Infrastructure Services och använda Azure AD Domain Services för identitet måste dessa program. Du kan välja att använda samma konto som ska synkroniseras från din lokala katalog till Azure AD. Alternativt kan du först skapa en anpassad Organisationsenhet och sedan skapa ett separat tjänstkonto i denna Organisationsenhet för att distribuera dessa program.

![Tjänstkonto som använder WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso har ett specialbyggt valvet program som innehåller en frontwebb, en SQLServer och backend FTP-servern. Windows-integrerad autentisering av tjänstkonton används för att autentisera frontwebb till FTP-servern. Webbklientservern har konfigurerats att köras som ett tjänstkonto. Backend-servern är konfigurerad för att tillåta åtkomst från kontot för frontwebben. Contoso föredrar att inte ska distribuera domain controller virtuell dator i molnet för att flytta programmet till Azure Infrastructure Services. Contosos IT-administratör kan distribuera servrarna där frontwebben, SQLServer och FTP-servern till Azure virtuella datorer. Dessa datorer ansluts sedan till en Azure AD Domain Services-hanterad domän. De kan sedan använda samma tjänstkonto i sina lokala kataloger för appens autentisering. Tjänstkontot synkroniseras till Azure AD Domain Services-hanterad domän och är tillgängliga för användning.

**Distributionsanteckningar**

Tänk på följande viktiga för det här distributionsscenariot:

* Kontrollera att programmet använder användarnamn/lösenord för autentisering. Certifikat/smartkort-baserad autentisering stöds inte av Azure AD Domain Services.
* Det går inte att ändra lösenord direkt mot den hanterade domänen. Användare kan ändra sina lösenord antingen med hjälp av Azure AD självbetjäning lösenord ändra mekanism eller i förhållande till den lokala katalogen. Dessa ändringar är automatiskt synkroniserats och finns tillgängliga i den hanterade domänen.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server Remote desktop services-distributioner i Azure
Du kan använda Azure AD Domain Services för att ge hanterad AD DS till din fjärrskrivbordsservrar som distribueras i Azure.

Mer information om det här distributionsscenariot finns så [integrera Azure AD Domain Services med RDS-distribution](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Domänanslutna HDInsight-kluster (förhandsgranskning)
Du kan ställa in ett Azure HDInsight-kluster som är ansluten till en Azure AD Domain Services-hanterad domän med Apache Ranger aktiverad. Skapa och tillämpa principer för Hive via Apache Ranger och ge användare (till exempel datavetare) kan ansluta till registreringsdatafilen med hjälp av ODBC-baserade verktyg, till exempel Excel, Tableau osv. Microsoft arbetar med att lägga till andra arbetsbelastningar, t.ex HBase, Spark och Storm, domänanslutna HDInsight snart.

Mer information om det här distributionsscenariot finns så [konfigurera domänanslutna HDInsight-kluster](../hdinsight/domain-joined/apache-domain-joined-configure.md)
