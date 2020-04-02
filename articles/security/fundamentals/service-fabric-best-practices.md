---
title: Metodtips för Azure Service Fabric-säkerhet
description: Den här artikeln innehåller en uppsättning metodtips för Azure Service Fabric-säkerhet.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: befe8945468d220a04ec7f0b515f22159cb72b0f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549234"
---
# <a name="azure-service-fabric-security-best-practices"></a>Metodtips för Azure Service Fabric-säkerhet
Det går snabbt, enkelt och kostnadseffektivt att distribuera ett program på Azure. Innan du distribuerar ditt molnprogram i produktion kan du läsa vår lista över viktiga och rekommenderade metodtips för att implementera säkra kluster i ditt program.

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara.

För varje bästa praxis förklarar vi:

-   Vad den bästa metoden är.
-   Varför du bör implementera bästa praxis.
-   Vad kan hända om du inte implementerar den bästa metoden.
-   Hur du kan lära dig att genomföra bästa praxis.

Vi rekommenderar följande metodtips för azure service fabric-säkerhet:

-   Använd Azure Resource Manager-mallar och PowerShell-modulen Service Fabric för att skapa säkra kluster.
-   Använd X.509-certifikat.
-   Konfigurera säkerhetsprinciper.
-   Implementera säkerhetskonfigurationen för Reliable Actors.
-   Konfigurera TLS för Azure Service Fabric.
-   Använd nätverksisolering och säkerhet med Azure Service Fabric.
-   Konfigurera Azure Key Vault för säkerhet.
-   Tilldela användare till roller.


## <a name="best-practices-for-securing-your-clusters"></a>Metodtips för att skydda dina kluster

Använd alltid ett säkert kluster:
-   Implementera klustersäkerhet med hjälp av certifikat.
-   Ange klientåtkomst (administratör och skrivskyddad) med hjälp av Azure Active Directory (Azure AD).

Använd automatiska distributioner:
-   Använd skript för att generera, distribuera och rulla över hemligheterna.
-   Lagra hemligheterna i Azure Key Vault och använd Azure AD för all annan klientåtkomst.
-   Kräv autentisering för mänsklig åtkomst till hemligheterna.

Tänk dessutom på följande konfigurationsalternativ:
-   Skapa perimeternätverk (kallas även demilitariserade zoner, DMZs och skärmade undernät) med hjälp av Azure Network Security Groups (NSG).
-   Få åtkomst till virtuella klusterdatorer (VMs) eller hantera klustret med hjälp av hoppservrar med anslutning till fjärrskrivbord.

Dina kluster måste skyddas för att förhindra att obehöriga användare ansluter, särskilt när ett kluster körs i produktion. Även om det är möjligt att skapa ett oskyddat kluster kan anonyma användare ansluta till klustret om klustret exponerar hanteringsslutpunkter för det offentliga internet.

Det finns tre [scenarier](../../service-fabric/service-fabric-cluster-security.md) för implementering av klustersäkerhet med hjälp av olika tekniker:

-   Nod-till-nodsäkerhet: Det här scenariot skyddar kommunikationen mellan de virtuella datorerna och datorerna i klustret. Den här formen av säkerhet säkerställer att endast de datorer som har behörighet att ansluta till klustret kan vara värdar för program och tjänster i klustret.
I det här fallet kan kluster som körs på Azure, eller fristående kluster som körs på Windows, använda antingen [certifikatsäkerhet](../../service-fabric/service-fabric-windows-cluster-x509-security.md) eller [Windows-säkerhet](../../service-fabric/service-fabric-windows-cluster-windows-security.md) för Windows Server-datorer.
-   Klient-till-nodsäkerhet: Det här scenariot skyddar kommunikationen mellan en Service Fabric-klient och de enskilda noderna i klustret.
-   Rollbaserad åtkomstkontroll (RBAC): I det här scenariot används separata identiteter (certifikat, Azure AD och så vidare) för varje administratörs- och användarklientroll som har åtkomst till klustret. Du anger rollidentiteterna när du skapar klustret.

>[!NOTE]
>**Säkerhetsrekommendation för Azure-kluster:** Använd Azure AD-säkerhet för att autentisera klienter och certifikat för nod-till-nodsäkerhet.

Mer om hur du konfigurerar ett fristående Windows-kluster finns i [Konfigurera inställningar för ett fristående Windows-kluster](../../service-fabric/service-fabric-cluster-manifest.md).

Använd Azure Resource Manager-mallar och PowerShell-modulen Service Fabric för att skapa ett säkert kluster.
Stegvisa instruktioner för att skapa ett säkert Service Fabric-kluster med hjälp av Azure Resource Manager-mallar finns i [Skapa ett Service Fabric-kluster](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Använd Azure Resource Manager-mallen:
-   Anpassa klustret med hjälp av mallen för att konfigurera hanterad lagring för virtuella hårddiskar (VMDs).
-   Driva ändringar i resursgruppen genom att använda mallen för enkel konfigurationshantering och granskning.

Behandla klusterkonfigurationen som kod:
-   Var noggrann när du kontrollerar distributionskonfigurationerna.
-   Undvik att använda implicita kommandon för att direkt ändra dina resurser.

Många aspekter av [programlivscykeln för Service Fabric](../../service-fabric/service-fabric-application-lifecycle.md) kan automatiseras. [Service Fabric PowerShell-modulen](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatiserar vanliga uppgifter för distribution, uppgradering, borttagning och testning av Azure Service Fabric-program. Hanterade API:er och HTTP-API:er för programhantering är också tillgängliga.

## <a name="use-x509-certificates"></a>Använda X.509-certifikat
Skydda alltid klustren med X.509-certifikat eller Windows-säkerhet. Säkerhet konfigureras endast vid skapande av kluster. Det går inte att aktivera säkerhet när klustret har skapats.

Om du vill ange ett [klustercertifikat](../../service-fabric/service-fabric-windows-cluster-x509-security.md)anger du värdet för **egenskapen ClusterCredentialType** till X509. Om du vill ange ett servercertifikat för externa anslutningar anger du egenskapen **ServerCredentialType** till X509.

Följ dessutom dessa metoder:
-   Skapa certifikat för produktionskluster med hjälp av en korrekt konfigurerad Windows Server-certifikattjänst. Du kan också hämta certifikaten från en godkänd certifikatutfärdare.
-   Använd aldrig ett tillfälligt certifikat eller testcertifikat för produktionskluster om certifikatet skapades med Hjälp av MakeCert.exe eller ett liknande verktyg.
-   Använd ett självsignerat certifikat för testkluster, men inte för produktionskluster.

Om klustret är osäkert kan vem som helst ansluta till klustret anonymt och utföra hanteringsåtgärder. Därför alltid säkra produktionskluster med hjälp av X.509-certifikat eller Windows-säkerhet.

Mer information om hur du använder X.509-certifikat finns i [Lägga till eller ta bort certifikat för ett Service Fabric-kluster](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Konfigurera säkerhetsprinciper
Service Fabric skyddar också de resurser som används av program. Resurser som filer, kataloger och certifikat lagras under användarkontona när programmet distribueras. Den här funktionen gör program som körs säkrare från varandra, även i en delad värdmiljö.

-   Använd en Active Directory-domängrupp eller -användare: Kör tjänsten under autentiseringsuppgifterna för ett Active Directory-användarkonto eller en Active Directory-användare. Var noga med att använda Active Directory lokalt inom domänen och inte Azure Active Directory. Få åtkomst till andra resurser i domänen som har beviljats behörighet med hjälp av en domänanvändare eller domängrupp. Till exempel resurser som filresurser.

-   Tilldela en säkerhetsåtkomstprincip för HTTP- och HTTPS-slutpunkter: Ange egenskapen **SecurityAccessPolicy** för att tillämpa en **RunAs-princip** på en tjänst när tjänstmanifestet deklarerar slutpunktsresurser med HTTP. Portar som allokerats till HTTP-slutpunkterna är korrekt åtkomstkontrollerade listor för det RunAs-användarkonto som tjänsten körs under. När principen inte har angetts har http.sys inte åtkomst till tjänsten och du kan få fel med samtal från klienten.

Mer information om hur du använder säkerhetsprinciper i ett Service Fabric-kluster finns i [Konfigurera säkerhetsprinciper för ditt program](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementera säkerhetskonfigurationen för Reliable Actors
Service Fabric Reliable Actors är en implementering av aktörens designmönster. Som med alla mönster för programvarudesign baseras beslutet att använda ett visst mönster på om ett programvaruproblem passar mönstret.

I allmänhet använder du aktörsdesignmönstret för att hjälpa till att modellera lösningar för följande programvaruproblem eller säkerhetsscenarier:
-   Ditt problemutrymme innebär ett stort antal (tusentals eller fler) av små, oberoende och isolerade enheter av tillstånd och logik.
-   Du arbetar med objekt med enkel tråd som inte kräver betydande interaktion från externa komponenter, inklusive frågetillstånd över en uppsättning aktörer.
-   Aktörsinstanserna blockerar inte anropare med oförutsägbara fördröjningar genom att utfärda I/O-åtgärder.

I Service Fabric implementeras aktörer i tillämpningsramverket för Reliable Actors. Detta ramverk är baserat på aktörsmönstret och bygger på [service fabric reliable services](../../service-fabric/service-fabric-reliable-services-introduction.md). Varje tillförlitlig aktörstjänst som du skriver är en partitionerad tillståndskänslig tillförlitlig tjänst.

Varje aktör definieras som en förekomst av en aktörstyp, identisk med hur ett .NET-objekt är en förekomst av en .NET-typ. En **aktörstyp** som implementerar funktionen för en kalkylator kan till exempel ha många aktörer av den typen som distribueras på olika noder i ett kluster. Var och en av de distribuerade aktörerna kännetecknas unikt av en aktör identifierare.

[Replikatorsäkerhetskonfigurationer](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) används för att skydda kommunikationskanalen som används under replikering. Den här konfigurationen förhindrar att tjänster ser varandras replikeringstrafik och säkerställer att data med högtillgänglig data är säkra. Som standard förhindrar ett tomt säkerhetskonfigurationsavsnitt replikeringssäkerhet.
Repliktorkonfigurationer konfigurerar replikatorn som är ansvarig för att göra tillståndet aktörstillstånd mycket tillförlitligt.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurera TLS för Azure-tjänstinfrastruktur
Serverautentiseringsprocessen [autentiserar](../../service-fabric/service-fabric-cluster-creation-via-arm.md) slutpunkterna för klusterhantering till en hanteringsklient. Hanteringsklienten inser sedan att den talar med det verkliga klustret. Det här certifikatet tillhandahåller också en [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) för HTTPS-hanterings-API:et och för Service Fabric Explorer via HTTPS.
Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn som du använder för klustret.

Om du vill konfigurera TLS för ett program måste du först skaffa ett SSL/TLS-certifikat som har signerats av en certifikatutfärdar. Certifikatutfärdaren är en betrodd tredje part som utfärdar certifikat för TLS-säkerhet. Om du inte redan har ett SSL/TLS-certifikat måste du skaffa ett från ett företag som säljer SSL/TLS-certifikat.

Certifikatet måste uppfylla följande krav för SSL/TLS-certifikat i Azure:
-   Certifikatet måste innehålla en privat nyckel.

-   Certifikatet måste skapas för nyckelutbyte och kunna exporteras till en fil för personligt informationsutbyte (.pfx).

-   Certifikatets ämnesnamn måste matcha domännamnet som används för att komma åt din molntjänst.

    - Skaffa ett anpassat domännamn som ska användas för att komma åt din molntjänst.
    - Begär ett certifikat från en certifikatutfärdar med ett ämnesnamn som matchar tjänstens anpassade domännamn. Om ditt eget domännamn till exempel är __contoso__**.com**ska certifikatet från certifikatutfärdaren ha ämnesnamnet **.contoso.com** eller __www__**.contoso.com**.

    >[!NOTE]
    >Du kan inte hämta ett SSL/TLS-certifikat från en certifikatutfärdar för cloudapp.net-domänen. __cloudapp__**.net**

-   Certifikatet måste använda minst 2 048-bitars kryptering.

HTTP-protokollet är osäkert och föremål för avlyssningsattacker. Data som överförs via HTTP skickas som oformaterad text från webbläsaren till webbservern eller mellan andra slutpunkter. Angripare kan avlyssna och visa känsliga data som skickas via HTTP, till exempel kreditkortsuppgifter och kontoinloggningar. När data skickas eller publiceras via en webbläsare via HTTPS säkerställer SSL att känslig information krypteras och skyddas från avlyssning.

Mer information om hur du använder SSL/TLS-certifikat finns i [Konfigurera TLS för ett program i Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Använda nätverksisolering och säkerhet med Azure Service Fabric
Konfigurera ett 3 nodetype säkert kluster med hjälp av [Azure Resource Manager-mallen](../../azure-resource-manager/templates/template-syntax.md) som ett exempel. Kontrollera inkommande och utgående nätverkstrafik med hjälp av mallen och nätverkssäkerhetsgrupperna.

Mallen har en NSG för var och en av skalningsuppsättningarna för den virtuella datorn och används för att styra trafiken in och ut ur uppsättningen. Reglerna konfigureras som standard för att tillåta all trafik som krävs för systemtjänsterna och de programportar som anges i mallen. Granska dessa regler och gör eventuella ändringar för att passa dina behov, inklusive att lägga till nya regler för dina program.

Mer information finns i [Vanliga nätverksscenarier för Azure Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurera Azure Key Vault för säkerhet
Service Fabric använder certifikat för att tillhandahålla autentisering och kryptering för att skydda ett kluster och dess program.

Service Fabric använder X.509-certifikat för att skydda ett kluster och för att tillhandahålla programsäkerhetsfunktioner. Du använder Azure Key Vault för att [hantera certifikat](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) för Service Fabric-kluster i Azure. Azure-resursprovidern som skapar kluster hämtar certifikaten från ett nyckelvalv. Providern installerar sedan certifikaten på de virtuella datorerna när klustret distribueras på Azure.

Det finns en certifikatrelation mellan [Azure Key Vault,](../../key-vault/key-vault-secure-your-key-vault.md)Service Fabric-klustret och resursprovidern som använder certifikaten. När klustret skapas lagras information om certifikatrelationen i ett nyckelvalv.

Det finns två grundläggande steg för att ställa in ett nyckelvalv:
1. Skapa en resursgrupp specifikt för nyckelvalvet.

    Vi rekommenderar att du placerar nyckelvalvet i en egen resursgrupp. Den här åtgärden hjälper till att förhindra förlust av nycklar och hemligheter om andra resursgrupper tas bort, till exempel lagring, beräkning eller gruppen som innehåller klustret. Resursgruppen som innehåller nyckelvalvet måste finnas i samma region som klustret som använder det.

2. Skapa ett nyckelvalv i den nya resursgruppen.

    Nyckelvalvet måste vara aktiverat för distribution. Beräkningsresursprovidern kan sedan hämta certifikaten från valvet och installera dem på VM-instanserna.

Mer information om hur du konfigurerar ett nyckelvalv finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md).

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat programmen för att representera klustret tilldelar du användarna till de roller som stöds av Service Fabric: skrivskyddad och administratör. Du kan tilldela dessa roller med hjälp av Azure-portalen.

>[!NOTE]
> Mer information om hur du använder roller i Service Fabric finns i [Rollbaserad åtkomstkontroll för Service Fabric-klienter](../../service-fabric/service-fabric-cluster-security-roles.md).

Azure Service Fabric stöder två åtkomstkontrolltyper för klienter som är anslutna till ett [Service Fabric-kluster:](../../service-fabric/service-fabric-cluster-creation-via-arm.md)administratör och användare. Klusteradministratören kan använda åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika användargrupper. Åtkomstkontroll gör klustret säkrare.

## <a name="next-steps"></a>Nästa steg

- [Checklista för serviceinfrastruktur](service-fabric-checklist.md)
- Konfigurera [utvecklingsmiljön](../../service-fabric/service-fabric-get-started.md)för Service Fabric .
- Läs mer om [supportalternativ för Service Fabric.](../../service-fabric/service-fabric-support.md)
