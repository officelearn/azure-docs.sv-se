---
title: Hanteringskoncept för Azure AD Domain Services | Microsoft-dokument
description: Lär dig mer om hur du administrerar en hanterad Azure Active Directory Domain Services-domän och hur användarkonton och lösenord fungerar
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: ba281ffb30801e0ae10cab10ceb95c0a3bffde2d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640011"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Hanteringsbegrepp för användarkonton, lösenord och administration i Azure Active Directory Domain Services

När du skapar och kör en AD DS-hanterad domän (Azure Active Directory Domain Services) finns det vissa skillnader i beteende jämfört med en traditionell lokal AD DS-miljö. Du använder samma administrationsverktyg i Azure AD DS som en självhanterad domän, men du kan inte direkt komma åt domänkontrollanterna (DC). Det finns också vissa skillnader i beteende för lösenordsprinciper och lösenordshare beroende på källan till att användarkontot skapas.

Den här begreppsmässiga artikeln beskriver hur du administrerar en Azure AD DS-hanterad domän och olika beteenden för användarkonton beroende på hur de skapas.

## <a name="domain-management"></a>Domänhantering

I Azure AD DS är domänkontrollanterna (DCs) som innehåller alla resurser som användare och grupper, autentiseringsuppgifter och principer en del av den hanterade tjänsten. För redundans skapas två domäner som en del av en Azure AD DS-hanterad domän. Du kan inte logga in på dessa DC för att utföra hanteringsuppgifter. I stället skapar du en hanterings-VM som är ansluten till Azure AD DS-hanterad domän och installerar sedan dina vanliga AD DS-hanteringsverktyg. Du kan använda snapin-modulerna Active Directory Administrationscenter eller Microsoft Management Console (MMC) som dns- eller grupprincipobjekt, till exempel.

## <a name="user-account-creation"></a>Skapa användarkonto

Användarkonton kan skapas i Azure AD DS på flera sätt. De flesta användarkonton synkroniseras från Azure AD, som också kan innehålla användarkonto synkroniserat från en lokal AD DS-miljö. Du kan också skapa konton manuellt direkt i Azure AD DS. Vissa funktioner, till exempel inledande lösenordssynkronisering eller lösenordsprincip, fungerar olika beroende på hur och var användarkonton skapas.

* Användarkontot kan synkroniseras från Azure AD. Detta inkluderar molnbaserade användarkonton som skapats direkt i Azure AD och hybridanvändarkonton synkroniserade från en lokal AD DS-miljö med Azure AD Connect.
    * Majoriteten av användarkontona i Azure AD DS skapas via synkroniseringsprocessen från Azure AD.
* Användarkontot kan skapas manuellt i en Azure AD DS-hanterad domän och finns inte i Azure AD.
    * Om du behöver skapa tjänstkonton för program som bara körs i Azure AD DS kan du skapa dem manuellt i den hanterade domänen. Eftersom synkronisering är ett sätt från Azure AD synkroniseras inte användarkonton som skapats i Azure AD DS tillbaka till Azure AD.

## <a name="password-policy"></a>Lösenordsprincip

Azure AD DS innehåller en standardordprincip som definierar inställningar för saker som kontoutelåsning, maximal lösenordsålder och lösenordskomplexitet. Inställningar som kontoutelåsningsprincipen gäller för alla användare i Azure AD DS, oavsett hur användaren skapades enligt beskrivningen i föregående avsnitt. Några inställningar, till exempel minsta lösenordslängd och lösenordskomplexitet, gäller endast användare som skapats direkt i Azure AD DS.

Du kan skapa egna anpassade lösenordsprinciper för att åsidosätta standardprincipen i Azure AD DS. Dessa anpassade principer kan sedan tillämpas på specifika grupper av användare efter behov.

Mer information om skillnaderna i hur lösenordsprinciper tillämpas beroende på källan för att skapa användare finns i [principer för lösenords- och kontoutelåsning på hanterade domäner][password-policy].

## <a name="password-hashes"></a>Lösenord hashar

För att autentisera användare på den hanterade domänen behöver Azure AD DS lösenordshashar i ett format som är lämpligt för NT LAN Manager (NTLM) och Kerberos-autentisering. Azure AD genererar inte eller lagrar lösenordsh hashar i det format som krävs för NTLM- eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhetsskäl lagrar Azure AD inte heller några lösenordsuppgifter i klartextform. Därför kan Azure AD inte automatiskt generera dessa NTLM- eller Kerberos-lösenordshashar baserat på användarnas befintliga autentiseringsuppgifter.

För användarkonton med enbart molnet måste användarna ändra sina lösenord innan de kan använda Azure AD DS. Den här lösenordsändringsprocessen gör att lösenordsh hashar för Kerberos- och NTLM-autentisering genereras och lagras i Azure AD. Kontot synkroniseras inte från Azure AD till Azure AD DS förrän lösenordet har ändrats.

För användare som synkroniseras från en lokal AD DS-miljö med Azure AD Connect [aktiverar du synkronisering av lösenordshashar][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect synkroniserar bara äldre lösenordshÃ¤ringar när du aktiverar Azure AD DS för din Azure AD-klientorganisation. Äldre lösenords hashar används inte om du bara använder Azure AD Connect för att synkronisera en lokal AD DS-miljö med Azure AD.
>
> Om dina äldre program inte använder NTLM-autentisering eller LDAP-enkla bindningar rekommenderar vi att du inaktiverar NTLM-lösenordshh-synkronisering för Azure AD DS. Mer information finns i [Inaktivera svaga chiffersviter och NTLM-hash-synkronisering för autentiseringsuppgifter][secure-domain].

När det är korrekt konfigurerat lagras de användbara lösenordsharen i azure AD DS-hanterade domänen. Om du tar bort den hanterade Azure AD DS-domänen tas alla lösenordshar som lagras vid den tidpunkten också bort. Synkroniserad autentiseringsuppgifter i Azure AD kan inte återanvändas om du senare skapar en Azure AD DS-hanterad domän - du måste konfigurera om synkroniseringen av lösenordsh hash-synkronisering för att lagra lösenords hasharen igen. Tidigare domänanslutna virtuella datorer eller användare kan inte autentiseras omedelbart – Azure AD måste generera och lagra lösenordshÃrna i den nya Azure AD DS-hanterade domänen. Mer information finns i [Synkroniseringsprocessen för lösenord hash för Azure AD DS och Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det stöds inte för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.

## <a name="forests-and-trusts"></a>Skogar och förtroenden

En *skog* är en logisk konstruktion som används av AD DS (Active Directory Domain Services) för att gruppera en eller flera *domäner*. Domänerna lagrar sedan objekt för användare eller grupper och tillhandahåller autentiseringstjänster.

I Azure AD DS innehåller skogen bara en domän. Lokala AD DS-skogar innehåller ofta många domäner. I stora organisationer, särskilt efter sammanslagningar och förvärv, kan du sluta med flera lokala skogar som var och en sedan innehåller flera domäner.

Som standard skapas en Azure AD DS-hanterad domän som en *användarskog.* Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användarkonton som skapats i en lokal AD DS-miljö. Användarkonton kan autentisera direkt mot azure AD DS-hanterad domän, till exempel för att logga in på en domänansluten virtuell dator. En användarskog fungerar när lösenordshã¤net kan synkroniseras och användarna inte använder exklusiva inloggningsmetoder som smartkortsautentisering.

I en Azure AD *DS-resursskog* autentiserar användare över ett enkelriktat *skogsförtroende* från sin lokala AD DS. Med den här metoden synkroniseras inte användarobjekt och lösenordsharhare till Azure AD DS. The user objects and credentials only exist in the on-premises AD DS. Med den här metoden kan företag vara värd för resurser och programplattformar i Azure som är beroende av klassisk autentisering, till exempel LDAPS, Kerberos eller NTLM, men eventuella autentiseringsproblem eller problem tas bort. Azure AD DS-resursskogar är för närvarande i förhandsversion.

Mer information om skogstyper i Azure AD DS finns i [Vad är resursskogar?][concepts-forest] [How do forest trusts work in Azure AD DS?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU:er

I Azure AD DS baseras de tillgängliga prestanda och funktioner på SKU. Du väljer en SKU när du skapar den hanterade domänen och du kan växla SKU:er när dina affärskrav ändras när den hanterade domänen har distribuerats. I följande tabell beskrivs de tillgängliga SKU:erna och skillnaderna mellan dem:

| SKU-namn   | Maximalt antal objekt | Säkerhetskopieringsfrekvens | Maximalt antal utgående skogsförtroende |
|------------|----------------------|------------------|----|
| Standard   | Obegränsat            | Var 7:e dag     | 0  |
| Enterprise | Obegränsat            | Var tredje dag     | 5  |
| Premium    | Obegränsat            | Varje dag            | 10 |

Innan dessa Azure AD DS SKU:er användes en faktureringsmodell baserad på antalet objekt (användar- och datorkonton) i azure AD DS-hanterade domänen. Det finns inte längre rörlig prissättning baserat på antalet objekt i den hanterade domänen.

Mer information finns på [sidan Azure AD DS-prissättning][pricing].

### <a name="managed-domain-performance"></a>Hanterad domänprestanda

Domänprestanda varierar beroende på hur autentisering implementeras för ett program. Ytterligare beräkningsresurser kan bidra till att förbättra frågesvarstiden och minska tiden i synkroniseringsåtgärder. När SKU-nivån ökar ökas beräkningsresurserna som är tillgängliga för den hanterade domänen. Övervaka prestanda för dina program och planera för de resurser som krävs.

Om ditt företag eller program kräver förändring och du behöver ytterligare beräkningskraft för din Azure AD DS-hanterade domän kan du växla till en annan SKU.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

Säkerhetskopieringsfrekvensen avgör hur ofta en ögonblicksbild av den hanterade domänen tas. Säkerhetskopior är en automatiserad process som hanteras av Azure-plattformen. I händelse av ett problem med din hanterade domän kan Azure-support hjälpa dig att återställa från säkerhetskopiering. Eftersom synkronisering bara sker på ett sätt *från* Azure AD påverkar eventuella problem i en Azure AD DS-hanterad domän inte Azure AD eller lokala AD DS-miljöer och funktioner.

När SKU-nivån ökar ökar frekvensen för dessa ögonblicksbilder av säkerhetskopiering. Granska dina affärskrav och återställningspunktsmål (RPO) för att fastställa den säkerhetskopieringsfrekvens som krävs för din hanterade domän. Om dina affärs- eller programkrav ändras och du behöver mer frekventa säkerhetskopior kan du växla till en annan SKU.

### <a name="outbound-forests"></a>Utgående skogar

I föregående avsnitt beskrivs utgående skogsförtroende från en Azure AD DS-hanterad domän till en lokal AD DS-miljö (för närvarande i förhandsversion). SKU avgör det maximala antalet skogsförtroende som du kan skapa för en Azure AD DS-hanterad domän. Granska dina affärs- och programkrav för att avgöra hur många förtroenden du faktiskt behöver och välj lämplig Azure AD DS SKU. Återigen, om dina affärskrav ändras och du behöver skapa ytterligare skogsförtroende kan du växla till en annan SKU.

## <a name="next-steps"></a>Nästa steg

Skapa en [Azure AD DS-hanterad domän][create-instance]för att komma igång .

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
