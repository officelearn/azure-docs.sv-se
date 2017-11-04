---
title: "Azure Service Fabric-säkerhetsmetoder | Microsoft Docs"
description: "Den här artikeln innehåller en uppsättning av bästa praxis för Azure Service Fabric-säkerhet."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: 682ad79cc5fe4f08051477b7b90ae80981e5d595
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric-säkerhetsmetoder
Distribuera ett program på Azure är snabb, enkel och kostnadseffektiv. Innan du distribuerar ditt moln program i produktion bör granska vår lista över viktiga och rekommenderade säkerhetsmetoder för att implementera skyddade kluster i ditt program.

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara. 

För varje bästa praxis förklarar vi:

-   Vad det är bra.
-   Varför bör du genomföra bästa praxis.
-   Vad som händer om du inte implementerar bästa praxis.
-   Hur kan du lära dig att implementera bästa praxis.

Vi rekommenderar följande Azure Service Fabric-säkerhet metoder:

-   Använda mallar för Azure Resource Manager och Service Fabric PowerShell-modulen för att skapa skyddade kluster.
-   Använd X.509-certifikat.
-   Konfigurera säkerhetsprinciper.
-   Implementera Reliable Actors säkerhetskonfiguration.
-   Konfigurera SSL för Azure Service Fabric.
-   Använda isolering av nätverk och säkerhet med Azure Service Fabric.
-   Konfigurera Azure Key Vault för säkerhet.
-   Tilldela användare till roller.


## <a name="best-practices-for-securing-your-clusters"></a>Metodtips för att skydda dina kluster

Använd alltid en säker klustret:
-   Implementera Klustersäkerhet med hjälp av certifikat.
-   Klientåtkomst (admin och skrivskyddade) med hjälp av Azure Active Directory (AD Azure).

Använd automatiserad distribution:
-   Använda skript för att skapa, distribuera och rulla över hemligheterna.
-   Lagra hemligheterna i Azure Key Vault och använda Azure AD för andra klientåtkomst.
-   Kräv autentisering för mänsklig åtkomst till hemligheterna.

Dessutom överväga följande konfigurationsalternativ:
-   Skapa perimeternätverk (även kallat demilitariserad zoner, DMZs och avskärmat undernät) med hjälp av Azure Nätverkssäkerhetsgrupper (NSG: er).
-   Åtkomst till klustret virtuella maskiner (VMs) eller hantera klustret med hjälp av hopp servrar med anslutning till fjärrskrivbord.

Dina kluster måste skyddas för att förhindra att obehöriga användare från att ansluta, särskilt när ett kluster som körs i produktion. Även om det är möjligt att skapa ett oskyddat kluster, kan anonyma användare ansluta till klustret om klustret exponerar hanteringsslutpunkter till internet.

Det finns tre [scenarier](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) för att implementera säkerhet för klustret med hjälp av olika tekniker:

-   Nod till nod säkerhet: det här scenariot skyddar kommunikationen mellan virtuella datorer och datorer i klustret. Den här typen av säkerhet säkerställer att endast de datorer som har behörighet att ansluta till klustret kan vara värd för program och tjänster i klustret.
I det här scenariot, som körs på Azure eller fristående kluster som körs på Windows kan använda antingen [certifikat säkerhet](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) eller [Windows-säkerhet](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security) för Windows Server-datorer.
-   Klient-till-nod säkerhet: det här scenariot skyddar kommunikationen mellan en klient för Service Fabric och enskilda noder i klustret.
-   Rollbaserad åtkomstkontroll (RBAC): det här scenariot använder separata identiteter (certifikat, Azure AD, och så vidare) för varje administratörs- och klient-roll som har åtkomst till klustret. Du kan ange roll identiteter när du skapar klustret.

>[!NOTE]
>**Säkerhetsrekommendation för Azure-kluster:** använda Azure AD-säkerhet för att autentisera klienter och certifikat för nod till nod säkerhet.

Om du vill konfigurera en fristående Windows-kluster, se [konfigurera inställningar för ett fristående Windows-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Använda mallar för Azure Resource Manager och Service Fabric PowerShell-modulen för att skapa en säker kluster.
Stegvisa instruktioner för att skapa en säker Service Fabric-kluster med hjälp av Azure Resource Manager-mallar, se [skapar ett Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Använd Azure Resource Manager-mallen:
-   Anpassa ditt kluster genom att använda mallen för att konfigurera hanterade lagringsutrymme för VM virtuella hårddiskar (VHD).
-   Enhet ändras till resursgruppen genom att använda mallen för enkelt konfigurationshantering och granskning.

Hantera klusterkonfigurationen som koden:
-   Vara noggrann när du kontrollerar din distributionskonfigurationer.
-   Undvik att använda implicita kommandon för att direkt ändra dina resurser.

Många aspekter av den [Service Fabric application livscykel](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) kan automatiseras. Den [Service Fabric PowerShell-modulen](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatiserar vanliga uppgifter för att distribuera, uppgradera, ta bort och testa Azure Service Fabric-program. Hanterade API: er och HTTP-APIs för programhantering finns också tillgängliga.

## <a name="use-x509-certificates"></a>Använd X.509-certifikat
Skydda dina kluster alltid med X.509-certifikat eller Windows-säkerhet. Säkerheten är bara konfigurerad vid tidpunkten för skapandet av klustret. Det går inte att aktivera säkerhet när klustret skapas.

Ange en [klustret certifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), ange värdet för den **ClusterCredentialType** egenskapen X509. Ange ett servercertifikat för utanför anslutningar, ange den **ServerCredentialType** egenskapen X509.

Dessutom gör dessa metoder:
-   Skapa certifikat för driftskluster med en korrekt konfigurerade tjänsten för Windows Server-certifikat. Du kan även hämta certifikat från en godkänd certifikatutfärdare (CA).
-   Använd aldrig en tillfällig eller testa certifikat för driftskluster om certifikatet har skapats med hjälp av MakeCert.exe eller ett liknande verktyg.
-   Använd ett självsignerat certifikat för testkluster, men inte för driftskluster.

Om klustret är oskyddade någon ansluta anonymt till klustret och utföra hanteringsåtgärder. Därför alltid skydda driftskluster med X.509-certifikat eller Windows-säkerhet.

Mer information om hur du använder X.509-certifikat finns [Lägg till eller ta bort certifikat för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Konfigurera säkerhetsprinciper
Service Fabric skyddar också de resurser som används av program. Resurser som filer, kataloger, och certifikat som lagras under användarkonton när programmet distribueras. Den här funktionen gör program som körs säkrare från varandra, även i en delad servermiljö.

-   Använda en Active Directory-domän, grupp eller användare: köra tjänsten under autentiseringsuppgifterna för ett Active Directory-användare eller grupp konto. Se till att använda Active Directory lokalt i din domän och inte Azure Active Directory. Komma åt andra resurser i domänen som har behörighet genom att använda en domänanvändare eller grupp. Till exempel resurser, till exempel filresurser.

-   Tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter: Ange den **SecurityAccessPolicy** egenskapen att tillämpa en **RunAs** principen till en tjänst när tjänstmanifestet deklarerar endpoint resurser med HTTP. Portar som allokerats till HTTP-slutpunkter är korrekt åtkomst kontrolleras listor för det RunAs-konto som tjänsten körs under. När principen inte anges http.sys har inte åtkomst till tjänsten och du kan hämta fel med anrop från klienten.

Information om hur du använder IPSec-principer i ett Service Fabric-kluster finns [konfigurera säkerhetsprinciper för ditt program](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementera säkerhetskonfiguration Reliable Actors
Service Fabric Reliable Actors är en implementering av aktören designmönstret. Precis som med alla programvara designmönstret baserat beslutet att använda ett specifikt mönster på om ett programfel passar mönstret.

I allmänhet Använd designmönstret aktören när modellen lösningar för följande programproblem eller säkerhetsscenarier:
-   Sidan problem innebär att ett stort antal (tusentalsavgränsare eller mer) liten, oberoende och isolerade enheter av tillstånd och logik.
-   Du arbetar med Enkeltrådig objekt som inte kräver betydande interaktion från externa komponenter, inklusive frågor tillstånd på en uppsättning aktörer.
-   Aktören-instanser blockera inte anropare med oväntade fördröjningar genom att utfärda o-åtgärder.

I Service Fabric implementeras aktörer i Reliable Actors application framework. Det här ramverket är baserat på aktören mönster och byggt ovanpå [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Varje tillförlitliga aktören-tjänst som du skriver är en partitionerad tillståndskänslig tillförlitlig tjänst.

Varje aktören definieras som en instans av aktörstyp, samma sätt som ett .NET-objekt är en instans av en .NET-typ. Till exempel en **aktören typen** som implementerar funktionerna i en kalkylator kan ha många aktörer av den typen som distribueras på olika noder i ett kluster. Var och en av de distribuerade aktörerna kännetecknas unikt av en aktören identifierare.

[Replikatorn säkerhetskonfigurationer](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) används för att skydda kommunikationskanalen som används vid replikering. Den här konfigurationen hindrar tjänster från att se varandras replikeringstrafik och säkerställer att högtillgänglig data är säkra. Som standard förhindrar en tom säkerhetskonfigurationsavsnittet replikeringssäkerhet.
Konfigurationer för replikatorn konfigurera replikatorn som ansvarar för att göra tillståndet aktören Tillståndsprovidern hög tillförlitlig.

## <a name="configure-ssl-for-azure-service-fabric"></a>Konfigurera SSL för Azure Service Fabric
Server-autentiseringen [autentiserar](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) att klustret management slutpunkter management-klienten. Management-klienten identifierar sedan att det pratar till verkliga klustret. Det här certifikatet ger också en [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) för HTTPS-hanterings-API och för Service Fabric Explorer via HTTPS.
Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Om du vill konfigurera SSL för ett program måste du först skaffa ett SSL-certifikat som har signerats av en Certifikatutfärdare. Certifikatutfärdaren är betrodd tredje part som utfärdar certifikat för SSL-säkerhetsskäl. Om du inte redan har ett SSL-certifikat, måste du skaffa ett från ett företag som säljer SSL-certifikat.

Certifikatet måste uppfylla följande krav för SSL-certifikat i Azure:
-   Certifikatet måste innehålla en privat nyckel.

-   Certifikatet måste skapas för nyckelutbyte och att exportera till en personal information exchange (.pfx)-fil.

-   Certifikatets ämnesnamn måste matcha det domännamn som används för åtkomst till Molntjänsten.

    - Hämta ett anpassat domännamn som ska användas för att komma åt Molntjänsten.
    - Begära ett certifikat från en Certifikatutfärdare med ett ämnesnamn som matchar din tjänst domännamn. Om ditt domännamn är till exempel __contoso__**.com**, certifikatet från Certifikatutfärdaren ska ha ämnesnamn **. contoso.com** eller __www__ **. contoso.com**.

    >[!NOTE]
    >Du kan skaffa ett SSL-certifikat från en Certifikatutfärdare för den __cloudapp__**.net** domän.
    
-   Certifikatet måste ha minst 2 048-bitarskryptering.

HTTP-protokollet är oskyddat och kan komma att avslyssna informationen. Data som överförs via HTTP skickas i klartext från webbläsaren till webbservern eller mellan slutpunkter. Angripare kan komma åt och visa känsliga data som skickas via HTTP, till exempel kreditkortsinformation och inloggningar på kontot. När data skickas eller anslås via en webbläsare via HTTPS, garanterar SSL att känslig information är krypterad och säker obehöriga.

Mer information om hur du använder SSL-certifikat finns [Konfigurera SSL för Azure-program](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Använda isolering av nätverk och säkerhet med Azure Service Fabric
Konfigurera en säker 3 nodetype-kluster med hjälp av den [Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) som ett exempel. Kontrollera inkommande och utgående nätverkstrafik genom att använda mallen och Nätverkssäkerhetsgrupper.

Mallen har en NSG för varje skaluppsättningar för virtuell dator och används för att styra trafiken till och från uppsättningen. Regler är konfigurerade som standard så att all trafik som krävs för systemtjänster och program-portar som angetts i mallen. Granska reglerna och göra ändringar så att de passar dina behov, inklusive att lägga till nya regler för dina program.

Mer information finns i [vanliga scenarier för nätverk för Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurera Azure Key Vault för säkerhet
Service Fabric använder certifikat för autentisering och kryptering för att skydda ett kluster och sina program.

Service Fabric använder X.509-certifikat skyddar ett kluster och säkerhetsfunktioner för programmet. Du använder Azure Key Vault till [hantera certifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) för Service Fabric-kluster i Azure. Azure-resursprovider som skapar kluster hämtar certifikat från en nyckelvalvet. Providern installeras certifikaten på virtuella datorer när klustret distribueras på Azure.

Det finns en certifikat-relation mellan [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Service Fabric-kluster och resursprovidern som använder certifikat. När klustret skapas lagras information om certifikat-relationen i en nyckelvalvet.

Det finns två grundläggande steg för att ställa in en nyckelvalv:
1. Skapa en resursgrupp för nyckelvalvet.

    Vi rekommenderar att du placerar nyckelvalvet i sin egen resursgruppen. Detta bidrar till att förhindra förlust av dina nycklar och hemligheter om andra resursgrupper tas bort, till exempel lagring, beräkning eller den grupp som innehåller klustret. Resursgruppen som innehåller ditt nyckelvalv måste vara i samma region som det kluster som använder den.

2. Skapa en nyckelvalvet i den nya resursgruppen.

    Nyckelvalvet måste aktiveras för distribution. Compute-resursprovidern kan få certifikat från valvet och installera dem på VM-instanser.

Läs mer om hur du ställer in ett nyckelvalv i [Kom igång med Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat de program som ska representera klustret tilldelar användarna till de roller som stöds av Service Fabric: skrivskyddade och administratör. Du kan tilldela dessa roller med hjälp av Azure portal.

>[!NOTE]
> Mer information om hur du använder roller i Service Fabric finns [rollbaserad åtkomstkontroll för Service Fabric-klienter](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric stöder två typer av åtkomstkontroll för klienter som är anslutna till en [Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administratörs- och. Klusteradministratören kan använda åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare. Åtkomstkontroll gör att klustret är säkrare.

## <a name="next-steps"></a>Nästa steg
- Konfigurera Service Fabric [utvecklingsmiljö](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Lär dig mer om [Service Fabric supportalternativ](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
