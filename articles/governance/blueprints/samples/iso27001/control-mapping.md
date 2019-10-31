---
title: ISO 27001 skiss exempel – kontroll mappning
description: Kontroll mappning av ISO 27001-skiss exemplet. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärderingen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/22/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: f187bf5a77f756bbc6293a14e1873d13788d3929
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162473"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>Kontroll mappning av ISO 27001-skiss exemplet

Följande artikel beskriver hur Azure-skisser ISO 27001 skiss exemplet mappar till ISO 27001-kontrollerna. Mer information om kontrollerna finns i [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Följande mappningar är till **ISO 27001:2013** -kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj **\[för hands versions\] granska ISO 27001:2013-kontroller och distribuera särskilda VM-tillägg för att ge stöd för det inbyggda princip initiativet för gransknings krav** .

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>En. 6.1.2-ansvars fördelning

Att ha bara en Azure-prenumerations ägare tillåter inte administrativ redundans. Att ha för många Azure-prenumerationer kan däremot öka risken för intrång via ett komprometterat ägar konto. Den här skissen hjälper dig att underhålla ett lämpligt antal prenumerations ägare i Azure genom att tilldela två [Azure policy](../../../policy/overview.md) definitioner som granskar antalet ägare för Azure-prenumerationer. Hantering av prenumerations ägarens behörigheter kan hjälpa dig att implementera lämplig uppdelning av uppgifter.

- \[för hands version\]: granska det lägsta antalet ägare för en prenumeration
- \[för hands version\]: granska det högsta antalet ägare för en prenumeration

## <a name="a821-classification-of-information"></a>En. 8.2.1-klassificering av information

Azures [tjänst för SQL sårbarhets bedömning](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) kan hjälpa dig att identifiera känsliga data som lagras i dina databaser och innehåller rekommendationer för att klassificera dessa data. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition för att granska att sårbarheter som identifieras under genomsökningen av SQL sårbarhets bedömning åtgärdas.

- \[för hands version\]: övervaka resultatet av SQL sårbarhets bedömning i Azure Security Center

## <a name="a912-access-to-networks-and-network-services"></a>En. 9.1.2-åtkomst till nätverk och nätverks tjänster

Azure implementerar [rollbaserad åtkomst kontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen hjälper dig att styra åtkomsten till Azure-resurser genom att tilldela sju [Azure policy](../../../policy/overview.md) definitioner. Dessa principer granskar användningen av resurs typer och konfigurationer som kan tillåta mer åtkomst till resurser.
Att förstå resurser som strider mot dessa principer kan hjälpa dig att vidta lämpliga åtgärder för att säkerställa åtkomst till Azure-resurser som är begränsade till behöriga användare.

- \[för hands version\]: Distribuera VM-tillägg för att granska Linux VM-konton utan lösen ord
- \[för hands version\]: Distribuera VM-tillägget till granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- \[för hands version\]: granska virtuella Linux-användarkonton utan lösen ord
- \[för hands version\]: granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Granska användningen av klassiska lagrings konton
- Granska användning av klassiska virtuella datorer
- Granska virtuella datorer som inte använder hanterade diskar

## <a name="a923-management-of-privileged-access-rights"></a>En. 9.2.3-hantering av privilegierade behörigheter

Den här skissen hjälper dig att begränsa och kontrol lera privilegierade åtkomst rättigheter genom att tilldela fyra [Azure policy](../../../policy/overview.md) -definitioner för att granska externa konton med ägare och/eller Skriv behörigheter och konton med ägare och/eller Skriv behörigheter som inte har Multi-Factor Authentication har Aktiver ATS. Azure implementerar rollbaserad åtkomst kontroll (RBAC) för att hantera vem som har åtkomst till Azure-resurser. Den här skissen tilldelar också tre Azure Policy definitioner för att granska användningen av Azure Active Directory autentisering för SQL-servrar och Service Fabric. Med hjälp av Azure Active Directory-autentisering möjliggörs förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster. Den här skissen tilldelar också en Azure Policy-definition för att granska användningen av anpassade RBAC-regler. Att förstå var anpassade RBAC-regler implementeras kan hjälpa dig att kontrol lera behovet och korrekt implementering eftersom anpassade RBAC-regler är fel känsliga.

- \[för hands version\]: granska konton med ägar behörigheter som inte är MFA-aktiverade för en prenumeration
- \[för hands version\]: granska konton med Skriv behörighet som inte är MFA-aktiverade för en prenumeration
- \[för hands version\]: granska externa konton med ägar behörigheter för en prenumeration
- \[för hands version\]: granska externa konton med Skriv behörighet för en prenumeration
- Granska etablering av en Azure Active Directory administratör för SQL Server
- Granska användning av Azure Active Directory för klientautentisering i Service Fabric
- Granska användningen av anpassade RBAC-regler

## <a name="a924-management-of-secret-authentication-information-of-users"></a>En. 9.2.4-hantering av information om hemlig autentisering av användare

Den här skissen tilldelar tre [Azure policy](../../../policy/overview.md) definitioner för granskning av konton som inte har Multi-Factor Authentication aktiverat. Multi-Factor Authentication hjälper till att skydda konton även om en viss autentiseringsinformation komprometteras. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare. Den här skissen tilldelar också två Azure Policy definitioner som granskar virtuella Linux-lösenords lösen ords fil behörigheter för att varna om de är felaktigt inställda. Med den här inställningen kan du vidta lämpliga åtgärder för att säkerställa att autentiserare inte komprometteras.

- \[för hands version\]: granska konton med ägar behörigheter som inte är MFA-aktiverade för en prenumeration
- \[för hands version\]: granska konton med Läs behörighet som inte är MFA-aktiverade för en prenumeration
- \[för hands version\]: granska konton med Skriv behörighet som inte är MFA-aktiverade för en prenumeration
- \[för hands version\]: Distribuera VM-tillägg för att granska virtuella Linux-filer passwd-filbehörighet
- \[för hands version\]: granskning av virtuella Linux-/etc/passwd fil behörigheter har angetts till 0644

## <a name="a925-review-of-user-access-rights"></a>En. 9.2.5-granskning av användar behörighet

Azure implementerar [rollbaserad åtkomst kontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar fyra [Azure policy](../../../policy/overview.md) definitioner till gransknings konton som ska prioriteras för granskning, inklusive avskrivna konton och externa konton med utökade behörigheter.

- \[för hands version\]: granska föråldrade konton på en prenumeration
- \[för hands version\]: granska föråldrade konton med ägar behörighet för en prenumeration
- \[för hands version\]: granska externa konton med ägar behörigheter för en prenumeration
- \[för hands version\]: granska externa konton med Skriv behörighet för en prenumeration

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 borttagning eller justering av åtkomst rättigheter

Azure implementerar [rollbaserad åtkomst kontroll](../../../../role-based-access-control/overview.md) (RBAC) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) och RBAC kan du uppdatera användar roller för att återspegla organisations ändringar. Vid behov kan konton blockeras från att logga in (eller tas bort), vilket omedelbart tar bort åtkomst behörighet till Azure-resurser. Den här skissen tilldelar två [Azure policy](../../../policy/overview.md) definitioner för att granska avskrivet konto som bör tas bort.

- \[för hands version\]: granska föråldrade konton på en prenumeration
- \[för hands version\]: granska föråldrade konton med ägar behörighet för en prenumeration

## <a name="a942-secure-log-on-procedures"></a>9\.4.2 säkra inloggnings procedurer

Den här skissen tilldelar tre Azure Policy definitioner för granskning av konton som inte har Multi-Factor Authentication aktiverat. Azure Multi-Factor Authentication ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare.

- \[för hands version\]: granska konton med ägar behörigheter som inte är MFA-aktiverade för en prenumeration
- \[för hands version\]: granska konton med Läs behörighet som inte är MFA-aktiverade för en prenumeration
- \[för hands version\]: granska konton med Skriv behörighet som inte är MFA-aktiverade för en prenumeration

## <a name="a943-password-management-system"></a>Ett. 9.4.3 lösen ords hanterings system

Den här skissen hjälper dig att använda starka lösen ord genom att tilldela 10 [Azure policy](../../../policy/overview.md) -definitioner som granskar virtuella Windows-datorer som inte uppfyller minimi kraven för styrka och andra lösen ord. Om de virtuella datorerna strider mot principen för lösen ords säkerhet kan du vidta åtgärder för att säkerställa att lösen ord för alla VM-användarkonton är kompatibla med principen.

- \[för hands version\]: Distribuera VM-tillägg för att granska virtuell Windows-dator Kräv krav för lösen ords komplexitet
- \[för hands version\]: Distribuera VM-tillägg till granska Windows VM högsta lösen ord ålder 70 dagar
- \[för hands version\]: Distribuera VM-tillägg till granska Windows VM lägsta lösen ord ålder för lösen ord 1 dag
- \[för hands version\]: Distribuera VM-tillägg för granskning av virtuella Windows-lösenord måste vara minst 14 tecken
- \[för hands version\]: Distribuera VM-tillägg för granskning av Windows VM bör inte tillåta tidigare 24 lösen ord
- \[för hands version\]: granska den virtuella Windows-datorn tillämpar krav på lösen ords komplexitet
- \[för hands version\]: granska den högsta tillåtna lösen ords åldern i Windows VM 70 dagar
- \[för hands version\]: granska minsta lösen ord ålder för Windows VM 1 dag
- \[för hands version\]: granskning av lösen ord för virtuella Windows-datorer måste vara minst 14 tecken
- \[för hands version\]: granska virtuell Windows-dator bör inte tillåta tidigare 24 lösen ord

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>En. 10.1.1-princip för användning av kryptografiska kontroller

Den här skissen hjälper dig att genomdriva principen om användningen av kryptografiska kontroller genom att tilldela 13 [Azure policy](../../../policy/overview.md) definitioner som tillämpar vissa kryptografiska kontroller och gransknings användningen av svaga krypterings inställningar.
Att förstå var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att resurserna konfigureras i enlighet med din informations säkerhets princip. Mer specifikt kräver principerna som tilldelats av den här skissen kryptering för Blob Storage-konton och data Lake Storage-konton. Kräv transparent data kryptering på SQL-databaser; granska saknad kryptering på lagrings konton, SQL-databaser, virtuella dator diskar och variabler för Automation-konton. granska oskyddade anslutningar till lagrings konton, Function-appar, webbappar, API Apps och Redis Cache; granska svag kryptering av lösen ord för virtuella datorer; och granska okrypterad Service Fabric kommunikation.

- \[för hands version\]: granska HTTPS endast åtkomst för en Funktionsapp
- \[för hands version\]: granska HTTPS endast åtkomst för ett webb program
- \[för hands version\]: granska HTTPS endast åtkomst för en API-app
- \[för hands version\]: granska saknad BLOB-kryptering för lagrings konton
- \[för hands version\]: Distribuera VM-tillägg för granskning av Windows VM ska inte lagra lösen ord med omvänd kryptering
- \[för hands version\]: granska virtuell Windows-dator ska inte lagra lösen ord med omvänd kryptering
- \[för hands version\]: övervaka okrypterade virtuella dator diskar i Azure Security Center
- Granska aktivering av kryptering av variabler för Automation-konton
- Granska aktivering av endast säkra anslutningar till din Redis Cache
- Granska säker överföring till lagrings konton
- Granska inställningen för egenskapen ClusterProtectionLevel till EncryptAndSign i Service Fabric
- Granska status för transparent datakryptering
- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="a1241-event-logging"></a>Händelse loggning för 12.4.1

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela sju [Azure policy](../../../policy/overview.md) definitioner som granskar logg inställningar på Azure-resurser.
Diagnostikloggar ger inblick i åtgärder som utfördes i Azure-resurser.

- \[för hands version\]: granska Dependency Agent distribution-VM avbildning (OS) har inte listats
- \[för hands version\]: granska Dependency Agent distribution i VMSS-VM-avbildningen (OS) har inte listats
- \[för hands version\]: granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- \[för hands version\]: granska Log Analytics agent distribution i VMSS-VM-avbildningen (OS) visas inte
- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- Granskning ska aktive ras för avancerade data säkerhets inställningar på SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>En. 12.4.3-administratör och operatörs loggar

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela sju Azure Policy definitioner som granskar logg inställningar på Azure-resurser. Diagnostikloggar ger inblick i åtgärder som utfördes i Azure-resurser.

- \[för hands version\]: granska Dependency Agent distribution-VM avbildning (OS) har inte listats
- \[för hands version\]: granska Dependency Agent distribution i VMSS-VM-avbildningen (OS) har inte listats
- \[för hands version\]: granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- \[för hands version\]: granska Log Analytics agent distribution i VMSS-VM-avbildningen (OS) visas inte
- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- Granskning ska aktive ras för avancerade data säkerhets inställningar på SQL Server

## <a name="a1244-clock-synchronization"></a>Synkronisering av 12.4.4-klockor

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela sju Azure Policy definitioner som granskar logg inställningar på Azure-resurser. Azure-loggar förlitar sig på synkroniserade interna klockor för att skapa en tidskorrelerad post av händelser mellan resurser.

- \[för hands version\]: granska Dependency Agent distribution-VM avbildning (OS) har inte listats
- \[för hands version\]: granska Dependency Agent distribution i VMSS-VM-avbildningen (OS) har inte listats
- \[för hands version\]: granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- \[för hands version\]: granska Log Analytics agent distribution i VMSS-VM-avbildningen (OS) visas inte
- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- Granskning ska aktive ras för avancerade data säkerhets inställningar på SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>En. 12.5.1-installation av program vara på operativa system

Anpassningsbar program kontroll är en lösning från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina virtuella datorer i Azure. Den här skissen tilldelar en Azure Policy-definition som övervakar ändringar i uppsättningen tillåtna program. Den här funktionen hjälper dig att styra installationen av program vara och program på virtuella Azure-datorer.

- \[för hands version\]: övervaka möjliga vit listning för appar i Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>En. 12.6.1-hantering av tekniska sårbarheter

Den här skissen hjälper dig att hantera problem med informations systemet genom att tilldela fem [Azure policy](../../../policy/overview.md) definitioner som övervakar saknade system uppdateringar, sårbarheter för operativ system, sårbarheter i SQL och virtuella dator sårbarheter i Azure Security Center. Azure Security Center tillhandahåller rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser.

- \[för hands version\]: övervaka saknade Endpoint Protection i Azure Security Center
- \[för hands version\]: övervaka saknade system uppdateringar i Azure Security Center
- \[för hands version\]: övervaka OS-sårbarheter i Azure Security Center
- \[för hands version\]: övervaka resultatet av SQL sårbarhets bedömning i Azure Security Center
- \[för hands version\]: övervaka säkerhets risker i virtuella datorer i Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>12.6.2-begränsningar för program varu installation

Anpassningsbar program kontroll är en lösning från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina virtuella datorer i Azure. Den här skissen tilldelar en Azure Policy-definition som övervakar ändringar i uppsättningen tillåtna program. Begränsningar för program varu installation kan hjälpa dig att minska sannolikheten för att program varu sårbarheter införs.

- \[för hands version\]: övervaka möjliga vit listning för appar i Azure Security Center

## <a name="a1311-network-controls"></a>13.1.1 nätverks kontroller

Den här skissen hjälper dig att hantera och styra nätverk genom att tilldela en [Azure policy](../../../policy/overview.md) definition som övervakar nätverks säkerhets grupper med tillåtna regler. Regler som är för tillåtelse kan tillåta oönskad nätverks åtkomst och bör granskas. Den här skissen tilldelar också tre Azure Policy definitioner som övervakar oskyddade slut punkter, program och lagrings konton. Slut punkter och program som inte skyddas av en brand vägg och lagrings konton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informations systemet.

- \[för hands version\]: övervaka åtkomst till nätverks åtkomst i Azure Security Center
- \[för hands version\]: övervaka oskyddade nätverks slut punkter i Azure Security Center
- \[för hands version\]: övervaka oskyddat webb program i Azure Security Center
- Granska obegränsad nätverks åtkomst till lagrings konton

## <a name="a1321-information-transfer-policies-and-procedures"></a>13.2.1 information överförings principer och procedurer

Skissen hjälper dig att se till att informations överföring med Azure-tjänster är säker genom att tilldela två [Azure policy](../../../policy/overview.md) definitioner för att granska oskyddade anslutningar till lagrings konton och Redis cache.

- Granska aktivering av endast säkra anslutningar till din Redis Cache
- Granska säker överföring till lagrings konton

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av ISO 27001-skissen kan du gå till följande artiklar om du vill lära dig mer om arkitekturen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Iso 27001 skiss – översikt](./index.md)
> [ISO 27001 skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om ritningar och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).