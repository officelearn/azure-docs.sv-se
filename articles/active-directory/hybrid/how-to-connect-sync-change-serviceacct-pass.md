---
title: 'Azure AD Connect-synkronisering:  Ändra Azure AD Connect Sync-tjänstkonto | Microsoft Docs'
description: Det här avsnittet dokumentet beskriver krypteringsnyckeln och hur du lämna den när lösenordet har ändrats.
services: active-directory
keywords: Azure AD sync-tjänstkontot, lösenord
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecc7eb45b439140cf9d1de048a6d4a7db48c34c3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204429"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Ändra Azure AD Connect sync tjänstkontolösenord
Om du ändrar Azure AD Connect sync tjänstkontolösenord kan synkroniseringstjänsten inte kan starta korrekt förrän du har avbrutit krypteringsnyckeln och initierats om Azure AD Connect sync tjänstkontolösenord. 

Azure AD Connect, som en del av Synkroniseringstjänsterna använder en krypteringsnyckel för att lagra lösenorden för tjänstkonton AD DS och AD Azure.  Dessa konton krypteras innan de lagras i databasen. 

Krypteringsnyckeln skyddas med [Windows DPAPI (Data Protection)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI skyddar den kryptering nyckeln med hjälp av den **lösenordet för Azure AD Connect-synkroniseringstjänstkontot**. 

Om du vill ändra lösenordet för tjänstens du kan använda procedurerna i [avbryts krypteringsnyckeln Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) att åstadkomma detta.  De här procedurerna bör också användas om du vill lämna krypteringsnyckeln av någon anledning.

## <a name="issues-that-arise-from-changing-the-password"></a>Problem som kan uppstå från att ändra lösenordet
Det finns två saker som du kan göra när du ändrar tjänstkontolösenord.

Först måste du ändra lösenordet under Windows Service Control Manager.  Tills problemet har lösts visas följande fel:


- Om du försöker starta synkroniseringstjänsten i Windows Service Control Manager du får felet ”**Windows kunde inte starta tjänsten Microsoft Azure AD Sync på lokal dator**”. **Fel 1069: Tjänsten kunde inte startas på grund av ett inloggningsfel.** "
- Under Windows Loggboken, systemhändelseloggen innehåller ett fel med **händelse-ID 7038** och meddelandet ”**ADSync-tjänsten kunde inte logga in precis som med aktuellt konfigurerat lösenord på grund av följande fel: Användarnamnet eller lösenordet är felaktigt.** "

Dessutom vissa villkor, kan om lösenordet uppdateras, synkroniseringstjänsten inte längre hämta krypteringsnyckeln via DPAPI. Synkroniseringstjänsten kan inte dekryptera de lösenord som krävs för att synkronisera till och från lokala AD och Azure AD utan krypteringsnyckeln.
Du ser fel som:

- Under Windows Service Control Manager om du försöker starta synkroniseringstjänsten och det går inte att hämta krypteringsnyckeln misslyckas med felet ”<strong>Windows kunde inte starta Microsoft Azure AD Sync på lokal dator. Granska systemhändelseloggen för mer information. Om det är en icke-Microsoft-tjänst kan kontakta leverantören för tjänsten och referera till tjänstspecifik felkod-21451857952</strong>”.
- Under Windows Loggboken, programmets händelselogg innehåller ett fel med **händelse-ID 6028** och felmeddelande *”**server-krypteringsnyckeln kan inte nås.**”*

För att säkerställa att du inte får de här felen, följer du procedurerna i [avbryts krypteringsnyckeln Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) när du ändrar lösenordet.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Lämna krypteringsnyckeln Azure AD Connect Sync
>[!IMPORTANT]
>Följande procedurer gäller endast för Azure AD Connect version 1.1.443.0 eller senare.

Använd följande procedurer för att lämna krypteringsnyckeln.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Så här gör du om du vill lämna krypteringsnyckeln

Om du vill lämna krypteringsnyckeln kan du använda följande procedurer för att åstadkomma detta.

1. [Stoppa synkroniseringstjänsten](#stop-the-synchronization-service)

1. [Avbryt den befintliga krypteringsnyckeln](#abandon-the-existing-encryption-key)

2. [Anger du lösenordet för AD DS-kontot](#provide-the-password-of-the-ad-ds-account)

3. [Initiera om lösenordet för Azure AD sync-konto](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Starta synkroniseringstjänsten](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Stoppa synkroniseringstjänsten
Du kan först stoppa tjänsten i Windows Service Control Manager.  Kontrollera att tjänsten inte körs vid försök att stoppa den.  Om den är, vänta tills den har slutförts och stoppa den.


1. Gå till Windows Service Control Manager (START → tjänster).
2. Välj **Microsoft Azure AD Sync** och klicka på Stoppa.

#### <a name="abandon-the-existing-encryption-key"></a>Avbryt den befintliga krypteringsnyckeln
Avbryt den befintliga krypteringsnyckeln så att du kan skapa den nya krypteringsnyckeln för:

1. Logga in på din Azure AD Connect-servern som administratör.

2. Starta en ny PowerShell-session.

3. Navigera till mappen: `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Kör kommandot: `./miiskmu.exe /a`

![Azure AD Connect Sync kryptering viktiga verktyg](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Anger du lösenordet för AD DS-kontot
Eftersom de befintliga lösenord som lagras i databasen kan inte längre dekrypteras så behöver du ge synkroniseringstjänsten lösenordet för AD DS-kontot. Synkroniseringstjänsten krypterar lösenord med hjälp av den nya krypteringsnyckeln:

1. Starta hanteraren för synkroniseringstjänsten (START → Synchronization Service).
</br>![Sync Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Gå till den **kopplingar** fliken.
3. Välj den **AD Connector** som motsvarar din lokala AD. Upprepa följande steg för var och en av dem om du har mer än en AD-koppling.
4. Under **åtgärder**väljer **egenskaper**.
5. I popup-dialogrutan Välj **Anslut till Active Directory-skog**:
6. Ange lösenordet för AD DS-konto i den **lösenord** textrutan. Om du inte vet lösenordet, måste du ange den till ett känt värde innan du utför det här steget.
7. Klicka på **OK** att spara det nya lösenordet och Stäng popup.
![Azure AD Connect Sync kryptering viktiga verktyg](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Initiera om lösenordet för Azure AD sync-konto
Du kan inte direkt anger du lösenordet för Azure AD-tjänstkontot till synkroniseringstjänsten. I stället måste du använda cmdlet: en **Lägg till ADSyncAADServiceAccount** att initiera om Azure AD-tjänstkontot. Cmdlet: en återställer lösenordet för och gör det tillgängligt för synkroniseringstjänsten:

1. Starta en ny PowerShell-session på Azure AD Connect-servern.
2. Kör cmdlet `Add-ADSyncAADServiceAccount`.
3. Ange autentiseringsuppgifter för Azure AD-Global administratör för Azure AD-klienten i popup-dialogrutan.
![Azure AD Connect Sync kryptering viktiga verktyg](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Om det lyckas visas PowerShell-Kommandotolken.

#### <a name="start-the-synchronization-service"></a>Starta synkroniseringstjänsten
Nu när synkroniseringstjänsten har åtkomst till krypteringsnyckeln och alla lösenord som behövs, du kan starta om tjänsten i Windows Service Control Manager:


1. Gå till Windows Service Control Manager (START → tjänster).
2. Välj **Microsoft Azure AD Sync** och klicka på Starta om.

## <a name="next-steps"></a>Nästa steg
**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
