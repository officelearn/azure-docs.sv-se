---
title: 'Azure AD Connect-synkronisering: Ändra ADSync-tjänstkontot | Microsoft-dokument'
description: I det här ämnesdokumentet beskrivs krypteringsnyckeln och hur du överger den när lösenordet har ändrats.
services: active-directory
keywords: Azure AD-synkroniseringstjänstkonto, lösenord
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
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077671ab4e964d7641aa3a0f0b435b39117eb6aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65139398"
---
# <a name="changing-the-adsync-service-account-password"></a>Ändra lösenordet för ADSync-tjänstkontot
Om du ändrar ADSync-tjänstkontots lösenord kan synkroniseringstjänsten inte starta korrekt förrän du har övergett krypteringsnyckeln och initierat adsync-tjänstkontots lösenord. 

Azure AD Connect, som en del av Synkroniseringstjänsterna använder en krypteringsnyckel för att lagra lösenorden för AD DS Connector-kontot och ADSync-tjänstkontot.  Dessa konton krypteras innan de lagras i databasen. 

Krypteringsnyckeln som används är skyddad med hjälp av [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI skyddar krypteringsnyckeln med **adsync-tjänstkontot**. 

Om du behöver ändra lösenordet för tjänstkontot kan du använda procedurerna för [att överge ADSync-tjänstkontots krypteringsnyckel](#abandoning-the-adsync-service-account-encryption-key) för att åstadkomma detta.  Dessa procedurer bör också användas om du behöver överge krypteringsnyckeln av någon anledning.

## <a name="issues-that-arise-from-changing-the-password"></a>Problem som uppstår vid ändring av lösenordet
Det finns två saker som måste göras när du ändrar lösenordet för tjänstkontot.

Först måste du ändra lösenordet under Windows Service Control Manager.  Tills problemet är löst kommer du att se följande fel:


- Om du försöker starta synkroniseringstjänsten i Windows Service Control Manager visas felet "**Windows kunde inte starta tjänsten Microsoft Azure AD Sync på lokal dator**". **Fel 1069: Tjänsten startade inte på grund av ett inloggningsfel.**"
- Under Windows Loggboken innehåller systemhändelseloggen ett fel med **händelse-ID 7038** och meddelandet "**ADSync-tjänsten kunde inte logga in som med det för närvarande konfigurerade lösenordet på grund av följande fel: Användarnamnet eller lösenordet är felaktigt.**"

För det andra, under särskilda förhållanden, om lösenordet uppdateras, kan synkroniseringstjänsten inte längre hämta krypteringsnyckeln via DPAPI. Utan krypteringsnyckeln kan synkroniseringstjänsten inte dekryptera de lösenord som krävs för att synkronisera till/från lokala AD och Azure AD.
Du kommer att se fel som:

- Om du försöker starta synkroniseringstjänsten och inte kan hämta krypteringsnyckeln under Windows Service Control Manager, misslyckas den med fel "<strong>Windows kunde inte starta Microsoft Azure AD Sync på den lokala datorn. Mer information finns i systemhändelseloggen. Om detta är en tjänst som inte kommer från Microsoft kontaktar du serviceleverantören och hänvisar till tjänstspecifik felkod -21451857952</strong>."
- Under Windows Loggboken innehåller programhändelseloggen ett fel med **händelse-ID 6028** och felmeddelandet *"Serverkrypteringsnyckeln kan inte nås".*

För att säkerställa att du inte får dessa fel följer du procedurerna för [att överge ADSync-tjänstkontots krypteringsnyckel](#abandoning-the-adsync-service-account-encryption-key) när du ändrar lösenordet.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Överger krypteringsnyckeln för ADSync-tjänstkonto
>[!IMPORTANT]
>Följande procedurer gäller endast för Azure AD Connect build 1.1.443.0 eller äldre.

Använd följande procedurer för att överge krypteringsnyckeln.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Så här gör du om du behöver överge krypteringsnyckeln

Om du behöver överge krypteringsnyckeln använder du följande procedurer för att åstadkomma detta.

1. [Stoppa synkroniseringstjänsten](#stop-the-synchronization-service)

1. [Överge den befintliga krypteringsnyckeln](#abandon-the-existing-encryption-key)

2. [Ange lösenordet för AD DS Connector-kontot](#provide-the-password-of-the-ad-ds-connector-account)

3. [Initiera lösenordet för ADSync-tjänstkontot](#reinitialize-the-password-of-the-adsync-service-account)

4. [Starta synkroniseringstjänsten](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Stoppa synkroniseringstjänsten
Först kan du stoppa tjänsten i Windows Service Control Manager.  Kontrollera att tjänsten inte körs när du försöker stoppa den.  Om det är, vänta tills den är klar och sedan stoppa den.


1. Gå till Windows Service Control Manager (START → Tjänster).
2. Välj **Microsoft Azure AD Sync** och klicka på Stoppa.

#### <a name="abandon-the-existing-encryption-key"></a>Överge den befintliga krypteringsnyckeln
Överge den befintliga krypteringsnyckeln så att ny krypteringsnyckel kan skapas:

1. Logga in på din Azure AD Connect-server som administratör.

2. Starta en ny PowerShell-session.

3. Navigera till mappen:`$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Kör kommandot:`./miiskmu.exe /a`

![Azure AD Connect-krypteringsnyckelverktyg](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Ange lösenordet för AD DS Connector-kontot
Eftersom de befintliga lösenord som lagras i databasen inte längre kan dekrypteras måste du ange synkroniseringstjänsten lösenordet för AD DS Connector-kontot. Synkroniseringstjänsten krypterar lösenorden med den nya krypteringsnyckeln:

1. Starta synkroniseringstjänsthanteraren (START → Synkroniseringstjänst).
</br>![Synkronisera tjänsthanteraren](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Gå till fliken **Kontakter.**
3. Välj den **AD-koppling** som motsvarar din lokala AD. Om du har mer än en AD-koppling upprepar du följande steg för var och en av dem.
4. Välj **Egenskaper**under **Åtgärder**.
5. Välj Anslut till Active **Directory Forest**i popup-dialogrutan:
6. Ange lösenordet för AD DS-kontot i textrutan **Lösenord.** Om du inte känner till lösenordet måste du ange det till ett känt värde innan du utför det här steget.
7. Klicka på **OK** om du vill spara det nya lösenordet och stänga popup-dialogrutan.
![Azure AD Connect-krypteringsnyckelverktyg](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Initiera lösenordet för ADSync-tjänstkontot
Du kan inte direkt ange lösenordet för Azure AD-tjänstkontot till synkroniseringstjänsten. I stället måste du använda cmdlet **Add-ADSyncAADServiceAccount** för att initiera om Azure AD-tjänstkontot. Cmdleten återställer kontolösenordet och gör det tillgängligt för synkroniseringstjänsten:

1. Starta en ny PowerShell-session på Azure AD Connect-servern.
2. Kör cmdlet `Add-ADSyncAADServiceAccount`.
3. I popup-dialogrutan anger du Azure AD Global-administratörsautentiseringsuppgifterna för din Azure AD-klientorganisation.
![Azure AD Connect-krypteringsnyckelverktyg](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Om det lyckas visas kommandotolken i PowerShell.

#### <a name="start-the-synchronization-service"></a>Starta synkroniseringstjänsten
Nu när synkroniseringstjänsten har åtkomst till krypteringsnyckeln och alla lösenord som behövs kan du starta om tjänsten i Windows Service Control Manager:


1. Gå till Windows Service Control Manager (START → Tjänster).
2. Välj **Microsoft Azure AD Sync** och klicka på Starta om.

## <a name="next-steps"></a>Nästa steg
**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
