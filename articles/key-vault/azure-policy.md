---
title: Integrera Azure Key Vault med Azure-principen
description: Lär dig hur du integrerar Azure Key Vault med Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 8c49b53cae08415633e1405317742a8a5d4e64b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196900"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrera Azure Key Vault med Azure-principen

[Azure Policy](../governance/policy/index.yml) är ett styrningsverktyg som ger användarna möjlighet att granska och hantera sin Azure-miljö i stor skala. Azure Policy ger möjlighet att placera skyddsräcken på Azure-resurser för att säkerställa att de är kompatibla med tilldelade principregler. Det gör det möjligt för användare att utföra granskning, realtidsförordning och reparation av sin Azure-miljö. Resultaten av granskningar som utförs av principen kommer att vara tillgängliga för användare i en instrumentpanel för efterlevnad där de kommer att kunna se en detaljerad granskning av vilka resurser och komponenter som är kompatibla och vilka som inte är det.  Mer information finns i [översikten över Azure Policy-tjänsten](../governance/policy/overview.md).

Exempel på användningsscenarier:

- Du vill förbättra företagets säkerhetsposition genom att implementera krav kring minsta nyckelstorlekar och maximala giltighetsperioder för certifikat i företagets nyckelvalv, men du vet inte vilka team som kommer att vara kompatibla och vilka som inte är det. 
- Du har för närvarande ingen lösning för att utföra en granskning i hela organisationen, eller så utför du manuella granskningar av din miljö genom att be enskilda team inom organisationen att rapportera deras efterlevnad. Du letar efter ett sätt att automatisera den här uppgiften, utföra granskningar i realtid och garantera riktigheten i revisionen.
- Du vill tillämpa företagets säkerhetsprinciper och hindra personer från att skapa självsignerade certifikat, men du har inget automatiserat sätt att blockera deras skapande. 
- Du vill lätta på vissa krav för dina testteam, men du vill behålla snäva kontroller över din produktionsmiljö. Du behöver ett enkelt automatiserat sätt att separera efterlevnaden av dina resurser. 
- Du vill vara säker på att du kan återställa efterlevnaden av nya principer i händelse av ett problem med en live-plats. Du behöver en lösning med ett klick för att inaktivera efterlevnaden av principen. 
- Du förlitar dig på en tredjepartslösning för granskning av din miljö och du vill använda ett internt Microsoft-erbjudande. 

## <a name="types-of-policy-effects-and-guidance"></a>Typer av politiska effekter och vägledning

**Granskning**: När effekten av en princip är inställd på granskning kommer principen inte att orsaka några brytande ändringar i din miljö. Du får bara en varning för komponenter som certifikat som inte följer principdefinitionerna inom ett angivet scope genom att markera dessa komponenter som icke-kompatibla i instrumentpanelen för principefterlevnad. Granskning är standard om ingen principeffekt har valts. 

**Neka**: När effekten av en princip är inställd på att neka, kommer principen att blockera skapandet av nya komponenter som certifikat samt blockera nya versioner av befintliga komponenter som inte överensstämmer med principdefinitionen. Befintliga icke-kompatibla resurser i ett nyckelvalv påverkas inte. Granskningsfunktionerna kommer att fortsätta att fungera.

## <a name="available-built-in-policy-definitions"></a>Tillgängliga principdefinitioner för "inbyggt"

Key Vault har skapat en uppsättning principer som du kan tilldela för vanliga scenarier för att hantera certifikat. Dessa principer är inbyggda, vilket innebär att de inte kräver att du skriver någon anpassad JSON för att aktivera dem och de är tillgängliga i Azure-portalen som du kan tilldela. Du kan fortfarande anpassa vissa parametrar så att de passar organisationens behov. 

De åtta förhandsgranskningsprinciperna är följande.

### <a name="manage-certificate-validity-period-preview"></a>Hantera certifikatets giltighetsperiod (förhandsgranskning)

Med den här principen kan du hantera den maximala giltighetstiden för certifikat som lagras i nyckelvalvet. Det är en god säkerhetspraxis att begränsa certifikatens maximala giltighetstid. Om en privat nyckel till certifikatet skulle äventyras utan identifiering minimerar kortlivade certifikat tidsramen för pågående skador och minskar värdet på certifikatet för en angripare. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Hantera tillåtna certifikatnyckeltyper (förhandsgranskning)
Med den här principen kan du begränsa vilken typ av certifikat som kan finnas i nyckelvalvet. Du kan använda den här principen för att se till att certifikatets privata nycklar är RSA, ECC eller HSM-stödda. Du kan välja bland följande lista vilka certifikattyper som är tillåtna.
- RSA
- RSA - HSM
- Ecc 
- ECC - HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Hantera utlösare av certifikatets livstid (förhandsgranskning)

Med den här principen kan du hantera den livstidsåtgärd som angetts för certifikat som antingen ligger inom ett visst antal dagar efter att de har upphört att gälla eller som har nått en viss procentandel av sin användbara livslängd. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Hantera certifikat som utfärdats av en integrerad certifikatutfärdare (förhandsversion)

Om du använder en integrerad certifikatutfärdare för Key Vault (Digicert eller GlobalSign) och du vill att användarna ska använda en eller någon av dessa leverantörer kan du använda den här principen för att granska eller framtvinga ditt val. Den här principen kan också användas för att granska eller neka skapandet av självsignerade certifikat i nyckelvalvet. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Hantera certifikat som utfärdats av en integrerad certifikatutfärdare (förhandsversion)

Om du använder en intern certifikatutfärdare eller en certifikatutfärdare som inte är integrerad med nyckelvalvet och du vill att användarna ska använda en certifikatutfärdare från en lista som du anger, kan du använda den här principen för att skapa en tillåten lista över certifikatutfärdare efter utfärdarnamn. Den här principen kan också användas för att granska eller neka skapandet av självsignerade certifikat i nyckelvalvet. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Hantera tillåtna kurvnamn för elliptiska kurvkryptograficertifikat (förhandsgranskning)
Om du använder elliptisk kurvkryptografi eller ECC-certifikat kan du anpassa en tillåten lista med kurvnamn från listan nedan. Standardalternativet tillåter alla följande kurvnamn. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Hantera minsta nyckelstorlek för RSA-certifikat (förhandsversion)
Om du använder RSA-certifikat kan du välja en minsta nyckelstorlek som certifikaten måste ha. Du kan välja ett alternativ i listan nedan. 
- 2048 bitar
- 3072 bitar
- 4096 bitar

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Hantera certifikat som finns inom ett angivet antal dagar efter förfallodatum (förhandsgranskning)
Tjänsten kan drabbas av ett avbrott om ett certifikat som inte övervakas tillräckligt inte roteras innan det upphör att gälla. Den här principen är avgörande för att se till att dina certifikat som lagras i nyckelvalv övervakas. Vi rekommenderar att du tillämpar den här principen flera gånger med olika tröskelvärden för förfallodatum, till exempel vid tröskelvärdena på 180, 90, 60 och 30 dagar. Den här principen kan användas för att övervaka och triage certifikat förfallodatum i din organisation. 

## <a name="example-scenario"></a>Exempel på ett scenario

Du hanterar ett nyckelvalv som används av flera team som innehåller 100 certifikat och du vill vara säker på att inget av certifikaten i nyckelvalvet är giltiga längre än 2 år.

1. Du tilldelar principen [Hantera certifikatets giltighetsperiod,](#manage-certificate-validity-period-preview) anger att den maximala giltighetstiden för ett certifikat är 24 månader och anger effekten av principen till "granskning". 
1. Du kan visa [efterlevnadsrapporten på Azure-portalen](#view-compliance-results)och upptäcka att 20 certifikat inte är kompatibla och giltiga i > 2 år och de återstående certifikaten är kompatibla. 
1. Du kontaktar ägarna till dessa certifikat och meddelar det nya säkerhetskravet att certifikat inte kan vara giltiga längre än 2 år. Vissa team svarar och 15 av certifikaten förnyades med en maximal giltighetstid på 2 år eller mindre. Andra team svarar inte och du har fortfarande fem certifikat som inte är kompatibla i nyckelvalvet.
1. Du ändrar effekten av den princip som du har tilldelat "neka". De 5 certifikat som inte uppfyller kraven återkallas inte och de fortsätter att fungera. De kan dock inte förnyas med en giltighetstid som är längre än två år. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Aktivera och hantera en Key Vault-princip via Azure-portalen

### <a name="select-a-policy-definition"></a>Välj en principdefinition

1. Logga in på Azure Portal. 
1. Sök "Policy" i sökfältet och välj **policy**.

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img1.png)

1. Välj **Definitioner**i fönstret Princip .

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img2.png)

1. Avmarkera **Markera alla** i kategorifiltret och välj **Nyckelvalv**. 

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img3.png)

1. Nu bör du kunna se alla principer som är tillgängliga för offentlig förhandsversion, för Azure Key Vault. Se till att du har läst och förstått avsnittet om principvägledning ovan och välj en princip som du vill tilldela ett scope.  

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Tilldela en princip till ett scope 

1. Välj en princip som du vill tillämpa i det här exemplet visas principen **Hantera certifikatgiltighetsperiod.** Klicka på tilldela-knappen i det övre vänstra hörnet.

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img5.png)
  
1. Välj den prenumeration där du vill att principen ska tillämpas. Du kan välja att begränsa scopet till endast en enskild resursgrupp i en prenumeration. Om du vill tillämpa principen på hela prenumerationen och utesluta vissa resursgrupper kan du också konfigurera en undantagslista. Ange att principbefogningsväljaren **ska aktiveras** om du vill att effekten av principen (granskning eller nekad) ska inträffa eller **Inaktiveras** för att inaktivera effekten (granskning eller neka). 

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img6.png)

1. Klicka på fliken parametrar högst upp på skärmen för att ange den maximala giltighetstiden i månader som du vill ha. Välj **granskning** eller **neka** för effekten av principen enligt vägledningen i avsnitten ovan. Välj sedan knappen granska + skapa. 

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img7.png)

### <a name="view-compliance-results"></a>Visa efterlevnadsresultat

1. Gå tillbaka till bladet Princip och välj fliken efterlevnad.

    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img8.png)

1. Från den här sidan kan du filtrera resultat efter kompatibla eller icke-kompatibla valv. Här kan du se en lista över icke-kompatibla nyckelvalv inom principtilldelningens omfattning. Ett valv anses inte kompatibelt om någon av komponenterna (certifikaten) i valvet inte är kompatibla. Du kan välja ett enskilt valv för att visa de enskilda icke-kompatibla komponenterna (certifikat). 


    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img9.png)

1. Visa namnet på komponenterna i ett valv som inte är kompatibla


    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img10.png)

1. Om du behöver kontrollera om användare nekas möjligheten att skapa resurser i nyckelvalvet kan du klicka på fliken **Komponenthändelser (förhandsversion)** för att visa en sammanfattning av nekade certifikatåtgärder med beställaren och tidsstämplarna för begäranden. 


    ![Översikt över hur Azure Key Vault fungerar](./media/policy-img11.png)

## <a name="feature-limitations"></a>Funktionsbegränsningar

Tilldela en princip med en "neka" effekt kan ta upp till 30 minuter (genomsnittligt fall) och 1 timme (värsta fall) för att börja neka skapandet av icke-kompatibla resurser. Principutvärderingen av befintliga komponenter i ett valv kan ta upp till 1 timme (genomsnittligt fall) och 2 timmar (värsta fall) innan efterlevnadsresultat kan visas i portalgränssnittet. Om efterlevnadsresultaten visas som "Inte startad" kan det bero på följande:
- Policyvärderingen har ännu inte slutförts. Inledande utvärdering svarstid kan ta upp till 2 timmar i det värsta scenariot. 
- Det finns inga nyckelvalv i principtilldelningens omfattning.
- Det finns inga nyckelvalv med certifikat inom principtilldelningens omfattning. 

## <a name="next-steps"></a>Efterföljande moment

- Läs mer om [Azure Policy-tjänsten](../governance/policy/overview.md)
- Se exempel på nyckelvalv: [Inbyggda principdefinitioner för Key Vault](../governance/policy/samples/built-in-policies.md#key-vault)