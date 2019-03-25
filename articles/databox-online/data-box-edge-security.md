---
title: Data Box Edge säkerhet | Microsoft Docs
description: Beskriver de funktioner för säkerhet och sekretess som skyddar din Data Box Edge-enhet, tjänst och data lokalt och i molnet.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 43de22f7e56178559df4fc45980d064962580d2b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403399"
---
# <a name="data-box-edge-security-and-data-protection"></a>Data Box Edge säkerhet och dataskydd

Säkerhet är av stor betydelse när en ny teknik, särskilt om tekniken används med konfidentiell eller upphovsrättsskyddad information. Microsoft Azure Data Box Edge lösningen säkerställer att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

Den här artikeln beskriver Data Box Edge-säkerhetsfunktioner som skyddar alla komponenter och data som lagras i dem.

Azure Data Box Edge-lösningen består av fyra huvudsakliga komponenter som interagerar med varandra:

- **Data Box Edge / Data Box-Gateway-tjänsten på Azure** – resursen för hantering som används för att skapa enheten ordning, konfigurera enheten och sedan spåra ordningen för slutförande.
- **Data Box-Edge-enhet** – överföringsenhet som levereras till dig att importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten** – klienter i din infrastruktur som ansluter till Data Box Edge-enhet och innehåller data som måste skyddas.
- **Molnlagring** – Platsen i Azure-molnet där data lagras. Den här platsen är vanligtvis storage-konto som är länkad till Data Box Edge-resurs som du skapade.


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Box Edge/Data Box Gateway-tjänsten för dataskydd

Data Box Edge/Data Box Gateway-tjänsten är en management-tjänst från Microsoft Azure. Tjänsten används för att konfigurera och hantera enheten.

- Åtkomst till Data Box Edge/Data Box Gateway-tjänsten kräver din organisation att ha ett Enterprise Agreement (EA) eller en prenumeration för Cloud Solution Provider (CSP). Mer information går du till [registrera dig för en Azure-prenumeration](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- Eftersom din management-tjänsten finns i Azure, är den skyddad med säkerhet i Azure-funktioner. Läs mer om säkerhetsfunktionerna i Microsoft Azure på [Microsoft Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/security/).

## <a name="data-box-edge-device-protection"></a>Skydd för data Box-Edge-enhet

Data Box Edge-enhet är en lokal enhet som hjälper dig att transformera data med bearbetning av lokalt och sedan skicka den till Azure. Din enhet:

- Behöver en aktiveringsnyckel åtkomst till Data Box Edge/Data Box Gateway-tjänsten.
- Skyddas vid alla tidpunkter av enhetens lösenord.
- Är en låst enhet. Enheten BMC och BIOS är lösenordsskyddad med begränsad användaråtkomst för BIOS.
- Har säker Start aktiverat.
- Kör Windows Defender Device Guard. Device Guard kan du köra de betrodda program som du definierar i dina kodintegritetsprinciper. 

### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiveringsnyckeln

Endast en auktoriserad Data Box Edge-enhet får ansluta till Data Box Edge/Data Box Gateway-tjänsten som du skapade i Azure-prenumerationen. För att auktorisera en enhet, måste du använda en aktiveringsnyckel för att aktivera enheten med Data Box Edge/Data Box Gateway-tjänsten. Mer information går du till [få en aktiveringsnyckel](data-box-edge-deploy-prep.md#get-the-activation-key).

Aktiveringsnyckeln som du använder:

- Är en nyckel för autentisering av Azure Active Directory (AAD).
- Upphör att gälla efter tre dagar.
- Används inte när enheten aktiveras.
 
När en enhet har aktiverats, använder token för att kommunicera med Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösenord

Lösenord kan du kontrollera att dina data är tillgängliga för auktoriserade användare. Data Box Edge och rutan Datagatewayen enheter startar upp en låst.

Du kan:

- Ansluta till det lokala webbgränssnittet på enheten via en webbläsare och ange sedan ett lösenord för att logga in på enheten.
- Fjärransluta till enheten PowerShell-gränssnittet via HTTP. Fjärrhantering är aktiverat som standard. Du kan sedan ange lösenordet för enheten att logga in på enheten. Mer information går du till [Anslut via en fjärranslutning till din Data Box Edge-enhet](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Tänk på följande metodtips:

- Data Box Edge-tjänsten kan inte hämta befintliga lösenord: den kan bara återställa dem via Azure portal. Vi rekommenderar att du lagrar alla lösenord på en säker plats så att du inte har att återställa ett lösenord om det glöms bort. Om du återställer ett lösenord, måste du meddela alla användare innan du återställa den.
- Använd lokalt webbgränssnitt till [ändra lösenordet](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Om du ändrar lösenordet, måste du meddela alla fjärranslutna användare så att de inte drabbas av ett tecken i fel.
- Du kan komma åt Windows PowerShell-gränssnittet på enheten via en fjärranslutning via HTTP. Som en säkerhetsåtgärd bör du använda HTTP betrodda nätverk.
- Kontrollera att enhetens lösenord är stark och väl skyddade. Följ den [Metodtips för lösenord](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>Skydda data

Det här avsnittet beskriver Data Box Edge-säkerhetsfunktioner som skyddar data under överföring och lagrade data.

### <a name="protect-data-at-rest"></a>Skydda data i vila

För de data vid vila:

- För de data vid vila använder Data Box Edge BitLocker XTS AES-256-kryptering för att skydda lokala data.
- För de data som finns i resurser, begränsas åtkomsten till resurser.

    - För SMB-klienter får åtkomst till filresurs-data, måste de autentiseringsuppgifter som är kopplade till resursen. Dessa autentiseringsuppgifter definieras på resursen skapas.
    - För NFS-klienter som har åtkomst till resurser, måste IP-adresserna för klienterna som ska läggas till i resursen skapas.


### <a name="protect-data-in-flight"></a>Skydda data som rör sig

För data-i-flygningens längd:

- Standard TLS 1.2 används för data som går mellan enheten och Azure. Det finns ingen reserv till TLS 1.1 och tidigare. Agentkommunikation kommer att blockeras om TLS 1.2 inte stöds. TLS 1.2 krävs också för portalen och SDK-hanteringsåtgärder.
- När klienterna kommer åt enheten via det lokala webbgränssnittet i en webbläsare, används som standard TLS 1.2 som standard säkert protokoll.

    - Det bästa sättet är att konfigurera din webbläsare för att använda TLS 1.2.
    - Om webbläsaren inte stöder TLS 1.2, kan du använda TLS 1.1 eller TLS 1.0.
- För att skydda data när du kopierar den från dina dataservrar, rekommenderar vi att du använder SMB 3.0 med kryptering.

### <a name="protect-data-via-storage-accounts"></a>Skydda data via storage-konton

Enheten är associerad med ett storage-konto som används som mål för dina data i Azure. Åtkomst till lagringskontot styrs av prenumerationen och lagring för två 512-bitars åtkomstnycklar som är associerade med det lagringskontot.

En av nycklarna används för autentisering när Data Box Edge-enhet har åtkomst till lagringskontot. Den andra nyckeln lagras i reserv, så att du kan rotera nycklarna regelbundet.

Av säkerhetsskäl kräver många Datacenter rotation av. Vi rekommenderar att du följer dessa Metodtips för rotation av:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Noggrant skydda din kontonyckel. Inte distribuera lösenordet till andra användare, hårt code den eller spara den var som helst i oformaterad textfil som andra har åtkomst till.
- [Återskapa din kontonyckel](../storage/common/storage-account-manage.md#regenerate-access-keys) med Azure portal om du tror att den komprometterats.
- Rotera och sedan [synkronisera dina lagringskontonycklar](data-box-gateway-manage-shares.md#sync-storage-keys) regelbundet för att säkerställa att ditt lagringskonto inte nås av obehöriga användare.
- Din Azure-administratör bör med jämna mellanrum, ändra eller återskapa den primära eller sekundära nyckeln med hjälp av avsnittet lagring i Azure portal för direkt åtkomst till lagringskontot.


## <a name="manage-personal-information"></a>Hantera personlig information

Data Box-Edge / Data Box-Gateway-tjänsten samlar in personlig information i följande viktiga instanser:

- **Orderdetaljer** – När ordern har skapats lagras leveransadressen, e-postadressen och kontaktuppgifterna för användare på Azure Portal. Informationen som sparas omfattar:
  - Kontaktnamn
  - Telefonnummer
  - E-post
  - Gatuadress
  - Ort
  - Postnummer
  - Status
  - Land/region
  - Spårningsnummer för leveransen

    Med orderinformationen krypteras och lagras i tjänsten. Informationen sparas i tjänsten tills du tar bort en resurs eller ordning uttryckligen. Dessutom blockeras borttagningen av resursen och motsvarande order tills enheten levereras tills återgår enheten till Microsoft.

- **Leveransadress** – när ordningen som placeras Data Box-tjänsten tillhandahåller leveransadressen till tredje parts-leverantörer, till exempel UPS.

- **Dela användare** -användare på din enhet kan också komma åt data som finns på filresurser. En lista över användare som har åtkomst till resursdata visas och kan visas. Den här listan är också tas bort när filresurser har tagits bort. Om du vill visa listan över användare som kan få åtkomst till eller ta bort en resurs, följer du stegen i [hantera filresurser på Data Box Edge](data-box-gateway-manage-shares.md).

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Data Box Edge-enhet](data-box-edge-deploy-prep.md).

