---
title: Azure Service Fabric-säkerhetsmetoder | Microsoft Docs
description: Den här artikeln innehåller en uppsättning Metodtips för Azure Service Fabric-säkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 8bafc4a95ca9af4567ed70c190a72f3b351da47c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611526"
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric-säkerhetsmetoder
Det är snabbt, enkelt och kostnadseffektivt att distribuera ett program på Azure. Läs igenom vår lista över viktiga och rekommenderade Metodtips för att implementera säkra kluster i ditt program innan du distribuerar ditt molnprogram i produktionen.

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara.

För varje rekommenderar förklarar vi:

-   Vad är det lämpligaste tillvägagångssättet.
-   Varför bör du implementera bästa praxis.
-   Vad som händer om du inte implementera bästa praxis.
-   Hur kan du lära dig att implementera bästa praxis.

Vi rekommenderar följande Azure Service Fabric-säkerhet metodtips:

-   Använd Azure Resource Manager-mallar och Service Fabric PowerShell-modulen för att skapa säkra kluster.
-   Använd X.509-certifikat.
-   Konfigurera säkerhetsprinciper.
-   Implementera Reliable Actors-säkerhetskonfiguration.
-   Konfigurera SSL för Azure Service Fabric.
-   Använd isolering av nätverk och säkerhet med Azure Service Fabric.
-   Konfigurera Azure Key Vault för säkerhet.
-   Tilldela användare till roller.


## <a name="best-practices-for-securing-your-clusters"></a>Metoder för att skydda dina kluster

Använd alltid ett säkert kluster:
-   Implementera Klustersäkerhet med hjälp av certifikat.
-   Ge klientåtkomst (admin och skrivskyddat) med hjälp av Azure Active Directory (AD Azure).

Använd automatisk distribution:
-   Du kan använda skript för att skapa, distribuera och förnyar hemligheterna.
-   Store hemligheterna i Azure Key Vault och använda Azure AD för alla andra klientåtkomst.
-   Kräv autentisering för mänskliga åtkomst till hemligheterna.

Överväg även följande konfigurationsalternativ:
-   Skapa perimeternätverk (även kallat demilitariserad zoner, DMZ-miljöer och avskärmat undernät) med hjälp av Azure-Nätverkssäkerhetsgrupper (NSG).
-   Få åtkomst till klustret virtuella datorer (VM) eller hantera ditt kluster med hjälp av jump-servrar med anslutning till fjärrskrivbord.

Dina kluster måste skyddas för att förhindra att obehöriga användare från att ansluta, särskilt när ett kluster körs i produktion. Även om det är möjligt att skapa ett oskyddat kluster kan anonyma användare ansluta till klustret om klustret exponerar hanteringsslutpunkter till det offentliga internet.

Det finns tre [scenarier](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) för att implementera Klustersäkerhet genom att använda olika tekniker:

-   Nod-till-nod-säkerhet: Det här scenariot skyddar kommunikationen mellan de virtuella datorerna och datorer i klustret. Den här typen av security säkerställer att endast de datorer som har behörighet att ansluta till klustret kan vara värd för program och tjänster i klustret.
I det här scenariot, kluster som körs på Azure eller fristående kluster som körs på Windows, kan använda antingen [certifikat security](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) eller [Windows security](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) för Windows Server-datorer.
-   Klient-till-nod-säkerhet: Det här scenariot skyddar kommunikationen mellan en Service Fabric-klient och enskilda noder i klustret.
-   Rollbaserad åtkomstkontroll (RBAC): Det här scenariot använder separata identiteter (certifikat, Azure AD, och så vidare) för varje administratörs- och klient-roll som har åtkomst till klustret. Du anger identiteterna som rollen när du skapar klustret.

>[!NOTE]
>**Säkerhetsrekommendation för Azure-kluster:** Använda Azure AD-säkerhetsgrupper för att autentisera klienter och certifikat för nod-till-nod-säkerhet.

För att konfigurera ett fristående Windows-kluster, se [konfigurera inställningar för ett fristående Windows-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Använda Azure Resource Manager-mallar och Service Fabric PowerShell-modulen för att skapa ett säkert kluster.
Stegvisa anvisningar om hur du skapar ett säkert Service Fabric-kluster med Azure Resource Manager-mallar finns i [skapar ett Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Använd Azure Resource Manager-mallen:
-   Anpassa ditt kluster med hjälp av mallen för att konfigurera hanterad lagring för virtuella datorer, virtuella hårddiskar (VHD).
-   Hantera ändringar i resursgruppen med hjälp av mallen för enkelt konfigurationshantering och granskning.

Hantera ditt klusterkonfiguration som kod:
-   Vara noggrann när du kontrollerar din distributionskonfigurationer.
-   Undvik att använda implicita kommandon direkt ändra dina resurser.

Många aspekter av den [livscykeln för Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) kan automatiseras. Den [Service Fabric PowerShell-modulen](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatiserar vanliga åtgärder för att distribuera, uppgradering, ta bort och testa Azure Service Fabric-program. Hanterade API: er och HTTP APIs för programhantering finns också.

## <a name="use-x509-certificates"></a>Använda X.509-certifikat
Skydda alltid dina kluster genom att använda X.509-certifikat eller Windows-säkerhet. Säkerhetsfunktioner konfigureras bara när kluster skapas. Det går inte att aktivera säkerhet när klustret har skapats.

Ange en [klustercertifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), ange värdet för den **ClusterCredentialType** X509 egenskap. Om du vill ange ett servercertifikat för utanför anslutningar, ange den **ServerCredentialType** X509 egenskap.

Dessutom kan följa dessa metoder:
-   Skapa certifikat för produktionskluster med en korrekt konfigurerad Certifikattjänsten för Windows Server. Du kan också hämta certifikaten från en godkänd certifikatutfärdare (CA).
-   Använd aldrig en tillfällig eller testa certifikat för produktionskluster om certifikatet har skapats med hjälp av MakeCert.exe eller ett liknande verktyg.
-   Använd ett självsignerat certifikat för testkluster, men inte för produktionskluster.

Om klustret är inte säkert, kan vem som helst ansluta till klustret anonymt och utföra hanteringsåtgärder. Därför alltid säkra produktionskluster med X.509-certifikat eller Windows-säkerhet.

Mer information om hur du använder X.509-certifikat finns [Lägg till eller ta bort certifikat för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Konfigurera säkerhetsprinciper
Service Fabric skyddar också de resurser som används av program. Resurser som filer, kataloger, och certifikat som lagras under användarkontona när programmet distribueras. Den här funktionen gör program som körs säkrare från varandra, även i en delad miljö.

-   Använd en Active Directory-domän, grupp eller användare: Köra tjänsten under autentiseringsuppgifterna för en Active Directory-användare eller gruppkonto. Glöm inte att använda Active Directory lokalt i din domän och inte Azure Active Directory. Komma åt andra resurser i domänen som har behörighet med hjälp av en domänanvändare eller grupp. Till exempel resurser, till exempel filresurser.

-   Tilldela en säkerhetsåtkomstprincip för slutpunkter för HTTP och HTTPS: Ange den **SecurityAccessPolicy** egenskapen att tillämpa en **RunAs** principen till en tjänst när tjänstmanifestet deklarerar endpoint-resurser med HTTP. Portar som allokerats till HTTP-slutpunkter är korrekt åtkomst-kontrollerade listor för det RunAs-konto som tjänsten körs under. När principen inte är konfigurerad http.sys har inte åtkomst till tjänsten och du kan få fel med anrop från klienten.

Läs hur du använder principer för säkerhet i Service Fabric-kluster i [ställer in säkerhetsprinciper för ditt program](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementera säkerhetskonfiguration Reliable Actors
Service Fabric Reliable Actors är en implementering av designmönstret aktör. Precis som med alla designmönster för programvara baseras beslutet att använda ett specifikt mönster på om ett programfel passar mönstret.

I allmänhet Använd designmönstret aktören när modellen lösningar för följande programproblem eller säkerhetsscenarier:
-   Ditt problem adressutrymme innebär att ett stort antal (tusentals eller mer) små, oberoende och isolerat enheter av tillstånd och logik.
-   Du arbetar med single-threaded-objekt som inte kräver betydande interaktion från externa komponenter, inklusive fråga tillstånd över en uppsättning aktörer.
-   Dina aktörsinstanser blockera inte anropare med oförutsägbara fördröjningar genom att utfärda i/o-åtgärder.

I Service Fabric implementeras actors inom ramen för Reliable Actors-programmet. Det här ramverket baseras på aktören mönstret och byggt ovanpå [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Varje reliable actor-tjänst som du skriver är en partitionerad tillståndskänslig tillförlitlig tjänst.

Varje aktör definieras som en instans av en typ av aktör, identiska med det sättet som ett .NET-objekt är en instans av en .NET-typ. Till exempel en **aktörstypen** att implementerar funktionerna i en kalkylator kan ha många aktörer av den typen som distribueras på olika noder i ett kluster. Var och en av distribuerade aktörer kännetecknas unikt av en aktör identifierare.

[Replikator säkerhetskonfigurationer](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) används för att skydda kommunikationskanalen som används under replikering. Den här konfigurationen hindrar tjänster från att se varandras replikeringstrafik och säkerställer att med hög tillgänglighet data är säkra. Som standard förhindrar en tom security konfigurationsavsnittet replikeringssäkerhet.
Replikator konfigurationer konfigurera replikatorn som ansvarar för att göra aktören Tillståndsprovider tillståndet mycket pålitlig.

## <a name="configure-ssl-for-azure-service-fabric"></a>Konfigurera SSL för Azure Service Fabric
Server-autentiseringsprocessen [autentiserar](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) slutpunkterna för klusterhantering i en Hanteringsklient. Management-klienten kan sedan identifiera att det pratar med verkligt kluster. Det här certifikatet ger också en [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) för HTTPS-hanterings-API och för Service Fabric Explorer över HTTPS.
Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för ditt kluster.

Om du vill konfigurera SSL för ett program, måste du först skaffa ett SSL-certifikat som har signerats av en Certifikatutfärdare. Certifikatutfärdaren är betrodd tredje part som utfärdar certifikat för SSL-säkerhetsskäl. Om du inte redan har ett SSL-certifikat, måste du skaffa ett från ett företag som säljer SSL-certifikat.

Certifikatet måste uppfylla följande krav för SSL-certifikat i Azure:
-   Certifikatet måste innehålla en privat nyckel.

-   Certifikatet måste skapas för nyckelutbyte och att exportera till en personal information exchange (.pfx)-fil.

-   Certifikatets ämnesnamn måste matcha det domännamn som används för att få åtkomst till din molntjänst.

    - Hämta ett anpassat domännamn för åtkomst till din molntjänst.
    - Begära ett certifikat från en Certifikatutfärdare med ett ämnesnamn som matchar eget domännamn för din tjänst. Exempel: om ditt domännamn är __contoso__**.com**, certifikatet från Certifikatutfärdaren måste ha namnet på certifikatmottagaren **. contoso.com** eller __www__ **. contoso.com**.

    >[!NOTE]
    >Du kan inte hämta ett SSL-certifikat från en Certifikatutfärdare för den __cloudapp__**.net** domän.

-   Certifikatet måste använda minst 2 048-bitarskryptering.

HTTP-protokollet är inte säkert och kan komma att avslyssna. Data som överförs via HTTP skickas som klartext från webbläsaren till webbservern eller mellan andra slutpunkter. Angripare kan fånga upp och visa känsliga data som skickas via HTTP, t.ex kreditkortsdata och inloggningar på kontot. När data skickas eller publiceras via en webbläsare via HTTPS, säkerställer SSL att känslig information är krypterad och säker obehöriga.

Mer information om hur du använder SSL-certifikat finns [Konfigurera SSL för Azure-program](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Använd isolering av nätverk och säkerhet med Azure Service Fabric
Konfigurera ett 3 nodetype säkra kluster med hjälp av den [Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) som ett exempel. Styr inkommande och utgående nätverkstrafik med hjälp av mallen och Nätverkssäkerhetsgrupper.

Mallen har en NSG för varje VM-skalningsuppsättningar och används för att styra trafiken in och ut ur uppsättningen. Reglerna är konfigurerade som standard som tillåter all trafik som krävs för systemtjänster och programportar som angetts i mallen. Granska reglerna och göra några ändringar så att de passar dina behov, inklusive att lägga till nya regler för dina program.

Mer information finns i [vanliga nätverksscenarier för Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurera Azure Key Vault för säkerhet
Service Fabric använder certifikat för autentisering och kryptering för att skydda ett kluster och dess program.

Service Fabric använder X.509-certifikat att skydda ett kluster och säkerhetsfunktioner för programmet. Du använder Azure Key Vault ska [hantera certifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) för Service Fabric-kluster i Azure. Azure-resursprovidern som skapar klustren hämtar certifikat från key vault. Providern installerar sedan certifikat på de virtuella datorerna när klustret distribueras på Azure.

Det finns en certifikat-relation mellan [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Service Fabric-klustret och resursprovidern som använder certifikat. När klustret har skapats lagras information om certifikat-relationen i ett nyckelvalv.

Det finns två grundläggande stegen för att ställa in ett nyckelvalv:
1. Skapa en resursgrupp specifikt för ditt nyckelvalv.

    Vi rekommenderar att du placerar i key vault i en egen resursgrupp. Detta bidrar till att förhindra förlust av dina nycklar och hemligheter om andra resursgrupper tas bort, till exempel lagring, beräkning eller den grupp som innehåller ditt kluster. Den resursgrupp som innehåller ditt nyckelvalv måste vara i samma region som klustret som använder den.

2. Skapa ett nyckelvalv i den nya resursgruppen.

    Nyckelvalvet måste aktiveras för distribution. Compute-resursprovidern kan sedan hämta certifikaten från valvet och installera dem på VM-instanser.

Läs mer om hur du konfigurerar ett nyckelvalv i [vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat de program som ska representera klustret kan tilldela dina användare till roller som stöds av Service Fabric: skrivskyddade och administratör. Du kan tilldela dessa roller med hjälp av Azure portal.

>[!NOTE]
> Mer information om hur du använder roller i Service Fabric finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric stöder två typer av åtkomstkontroll för klienter som är anslutna till en [Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administratör och användare. Klusteradministratören kan använda åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare. Åtkomstkontroll blir klustret säkrare.

## <a name="next-steps"></a>Nästa steg

- [Checklista för Service Fabric-säkerhet](azure-service-fabric-security-checklist.md)
- Ställ in din Service Fabric [utvecklingsmiljö](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Lär dig mer om [Service Fabric-supportalternativ](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
