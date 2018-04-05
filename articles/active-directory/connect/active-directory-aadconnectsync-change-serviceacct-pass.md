---
title: 'Azure AD Connect-synkronisering: ändra tjänstkontot för Azure AD Connect Sync | Microsoft Docs'
description: Här avsnittet beskrivs krypteringsnyckeln och hur du avbryta den när lösenordet ändras.
services: active-directory
keywords: Azure AD-synkroniseringstjänstkontot, lösenord
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: faa6d667b514563516c23e0484437ac9572e52b7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Ändra lösenord för tjänstkonto i Azure AD Connect-synkronisering
Om du ändrar lösenord för tjänstkonto i Azure AD Connect sync kommer synkroniseringstjänsten inte att kunna starta korrekt förrän du har avbrutna krypteringsnyckeln och initieras på nytt lösenord för tjänstkonto i Azure AD Connect-synkronisering. 

Azure AD Connect, som en del av Synkroniseringstjänsterna använder en krypteringsnyckel för att lagra lösenord i AD DS och AD Azure-tjänstkonton.  Dessa konton krypteras innan de lagras i databasen. 

Krypteringsnyckeln är säkrad via [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI skyddar en kryptering nyckel med hjälp av den **lösenordet för tjänstkontot för Azure AD Connect sync**. 

Om du behöver ändra tjänstkontots lösenord du kan använda procedurerna i [överges krypteringsnyckeln Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) att åstadkomma detta.  De här procedurerna bör också användas om du behöver avbryta krypteringsnyckeln av någon anledning.

## <a name="issues-that-arise-from-changing-the-password"></a>Problem som uppstår när du ändrar lösenordet
Det finns två saker som behöver göras när du ändrar tjänstkontots lösenord.

Du måste först ändra lösenordet under Windows Service Control Manager.  Tills problemet är löst visas följande fel:


- Om du försöker starta synkroniseringstjänsten i Windows Service Control Manager felmeddelandet ”**Windows kunde inte starta tjänsten Microsoft Azure AD Sync på lokal dator**”. **Fel 1069: Tjänsten kunde inte starta på grund av ett inloggningsfel.** "
- Under Windows Loggboken systemets händelselogg innehåller ett fel med **händelse-ID 7038** och meddelandet ”**i ADSync-tjänsten kunde inte logga in som med aktuellt konfigurerat lösenord på grund av följande fel: användarnamnet eller lösenordet är felaktigt.**”

Andra kan vissa villkor kan om lösenordet uppdateras synkroniseringstjänsten inte längre hämta krypteringsnyckeln via DPAPI. Synkroniseringstjänsten kan inte dekryptera lösenord krävs för att synkronisera till och från lokala AD och Azure AD utan krypteringsnyckeln.
Du ser fel som:

- Under Windows Service Control Manager om du försöker starta synkroniseringstjänsten och det går inte att hämta krypteringsnyckeln misslyckas med felet ”**Windows kunde inte starta Microsoft Azure AD Sync på den lokala datorn. Mer information finns i händelseloggen System. Om detta är en icke-Microsoft-tjänst Kontakta leverantören för tjänsten och tjänstspecifika felkoden **-21451857952****.”
- Under Windows Loggboken i programmets händelselogg innehåller ett fel med **händelse-ID 6028** och felmeddelande *”**krypteringsnyckeln server kan inte nås.**”*

För att säkerställa att du inte får dessa fel, följer du procedurerna i [överges krypteringsnyckeln Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) när du ändrar lösenordet.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Överges krypteringsnyckeln Azure AD Connect Sync
>[!IMPORTANT]
>Följande procedurer gäller endast för Azure AD Connect build 1.1.443.0 eller äldre.

Använd följande procedurer för att undvika krypteringsnyckeln.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Vad du gör om du behöver avbryta krypteringsnyckeln

Om du behöver avbryta krypteringsnyckeln kan du använda följande procedurer för att åstadkomma detta.

1. [Avbryt befintliga krypteringsnyckeln](#abandon-the-existing-encryption-key)

2. [Ange lösenordet för AD DS-konto](#provide-the-password-of-the-ad-ds-account)

3. [Initiera om lösenordet för Azure AD sync-kontot](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Starta synkroniseringstjänsten](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Avbryt befintliga krypteringsnyckeln
Avbryt den befintliga krypteringsnyckeln så att nya krypteringsnyckeln kan skapas:

1. Logga in på ditt Azure AD Connect-servern som administratör.

2. Starta en ny PowerShell-session.

3. Navigera till mappen: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Kör kommandot: `./miiskmu.exe /a`

![Azure AD Connect Sync kryptering viktiga verktyg](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Ange lösenordet för AD DS-konto
Som de befintliga lösenord som lagrats i databasen kan inte längre dekrypteras så behöver du ge synkroniseringstjänsten lösenordet för AD DS-konto. Synkroniseringstjänsten krypterar lösenord med hjälp av den nya krypteringsnyckeln:

1. Starta hanteraren för synkroniseringstjänsten (START → synkroniseringstjänsten).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Gå till den **kopplingar** fliken.
3. Välj den **AD-koppling** som motsvarar dina lokala AD. Upprepa följande steg för dem om du har flera AD-koppling.
4. Under **åtgärder**väljer **egenskaper**.
5. I popup-fönstret väljer **Anslut till Active Directory-skog**:
6. Ange lösenordet för AD DS-konto i den **lösenord** textruta. Om du inte vet lösenordet måste du ange den till ett känt värde innan du utför det här steget.
7. Klicka på **OK** att spara det nya lösenordet och stänga popup-fönstret.
![Azure AD Connect Sync kryptering viktiga verktyg](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Initiera om lösenordet för Azure AD sync-kontot
Du kan inte direkt ange lösenordet för Azure AD-tjänstkontot till synkroniseringstjänsten. I stället måste du använda cmdlet **Lägg till ADSyncAADServiceAccount** att initiera om Azure AD-tjänstkontot. Cmdlet: en återställer kontolösenordet och gör den tillgänglig för synkroniseringstjänsten:

1. Starta en ny PowerShell-session på Azure AD Connect-servern.
2. Kör cmdlet `Add-ADSyncAADServiceAccount`.
3. Ange autentiseringsuppgifter för Azure AD-Global administratör för Azure AD-klienten i popup-fönstret.
![Azure AD Connect Sync kryptering viktiga verktyg](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Om det lyckas visas PowerShell-Kommandotolken.

#### <a name="start-the-synchronization-service"></a>Starta synkroniseringstjänsten
Nu när synkroniseringstjänsten har åtkomst till krypteringsnyckeln och alla lösenord som behövs, du kan starta om tjänsten i Windows Service Control Manager:


1. Gå till Windows Service Control Manager (START → Services).
2. Välj **Microsoft Azure AD Sync** och klickar på Starta om.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
