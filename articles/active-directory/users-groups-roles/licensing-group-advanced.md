---
title: Gruppbaserade ytterligare scenarier för licensiering – Azure AD | Microsoft-dokument
description: Fler scenarier för Azure Active Directory-gruppbaserad licensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139d7e0cf2b57cc466dc97370b90a599257ce755
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266291"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenarier, begränsningar och kända problem med hjälp av grupper för att hantera licensiering i Azure Active Directory

Använd följande information och exempel för att få en mer avancerad förståelse för Azure Active Directory (Azure AD) gruppbaserad licensiering.

## <a name="usage-location"></a>Användningsplats

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen **Användningsplats** för användaren. I [Azure-portalen](https://portal.azure.com)kan du ange användningsplats i &gt; **User** &gt; **Användarprofilinställningar** . **Settings**

För grupplicenstilldelning ärver alla användare utan angiven användningsplats platsen för katalogen. Om du har användare på flera platser måste du se till att återspegla det korrekt i dina användarresurser innan du lägger till användare i grupper med licenser.

> [!NOTE]
> Grupplicenstilldelningen ändrar aldrig ett befintligt användningsplatsvärde för en användare. Vi rekommenderar att du alltid anger användningsplats som en del av ditt användarskapande flöde i Azure AD (t.ex. via AAD Connect-konfiguration) - som säkerställer att resultatet av licenstilldelning alltid är korrekt och att användarna inte får tjänster på platser som inte är tillåtna.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Använda gruppbaserad licensiering med dynamiska grupper

Du kan använda gruppbaserad licensiering med valfri säkerhetsgrupp, vilket innebär att den kan kombineras med dynamiska Azure AD-grupper. Dynamiska grupper kör regler mot användarresursattribut för att automatiskt lägga till och ta bort användare från grupper.

Du kan till exempel skapa en dynamisk grupp för vissa uppsättning produkter som du vill tilldela användarna. Varje grupp fylls i av en regel som lägger till användare med sina attribut, och varje grupp tilldelas de licenser som du vill att de ska ta emot. Du kan tilldela attributet lokalt och synkronisera det med Azure AD, eller så kan du hantera attributet direkt i molnet.

Licenser tilldelas användaren strax efter att de har lagts till i gruppen. När attributet ändras lämnar användaren grupperna och licenserna tas bort.

### <a name="example"></a>Exempel

Tänk på exemplet med en lokal identitetshanteringslösning som bestämmer vilka användare som ska ha åtkomst till Microsofts webbtjänster. Den använder **extensionAttribute1** för att lagra ett strängvärde som representerar de licenser som användaren bör ha. Azure AD Connect synkroniserar det med Azure AD.

Användare kan behöva en licens men inte en annan, eller kan behöva båda. Här är ett exempel där du distribuerar Office 365 Enterprise E5- och Enterprise Mobility + Security (EMS) licenser till användare i grupper:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: bastjänster

![Skärmbild av Office 365 Enterprise E5-bastjänster](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licensierade användare

![Skärmbild av användare av Enterprise Mobility + Security](./media/licensing-group-advanced/o365-e5-licensed-users.png)

I det här exemplet ändrar du en användare och `EMS;E5_baseservices;` anger deras tilläggAttribut1 till värdet för om du vill att användaren ska ha båda licenserna. Du kan göra den här ändringen lokalt. När ändringen synkroniseras med molnet läggs användaren automatiskt till i båda grupperna och licenser tilldelas.

![Skärmbild som visar hur du ställer in användarens tilläggAttribut1](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Var försiktig när du ändrar en befintlig grupps medlemsregel. När en regel ändras utvärderas gruppens medlemskap och användare som inte längre matchar den nya regeln tas bort (användare som fortfarande matchar den nya regeln påverkas inte under den här processen). Dessa användare kommer att få sina licenser borttagna under processen som kan leda till förlust av tjänsten, eller i vissa fall, förlust av data.
> 
> Om du har en stor dynamisk grupp som du är beroende av för licenstilldelning kan du överväga att validera alla större ändringar i en mindre testgrupp innan du tillämpar dem på huvudgruppen.

## <a name="multiple-groups-and-multiple-licenses"></a>Flera grupper och flera licenser

En användare kan vara medlem i flera grupper med licenser. Här är några saker att tänka på:

- Flera licenser för samma produkt kan överlappa varandra och de resulterar i att alla aktiverade tjänster tillämpas på användaren. I följande exempel visas två licensieringsgrupper: *E3-bastjänsterna* innehåller grundtjänsterna som ska distribueras först, till alla användare. Och *E3 utökade tjänster* innehåller ytterligare tjänster (Sway och Planner) som endast distribueras till vissa användare. I det här exemplet lades användaren till i båda grupperna:

  ![Skärmbild av aktiverade tjänster](./media/licensing-group-advanced/view-enabled-services.png)

  Som ett resultat har användaren 7 av de 12 tjänsterna i produkten aktiverade, när du använder endast en licens för den här produkten.

- Om du väljer *E3-licensen* visas mer information, inklusive information om vilka tjänster som är aktiverade för användaren av grupplicenstilldelningen.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Direkta licenser samexisterar med grupplicenser

När en användare ärver en licens från en grupp kan du inte direkt ta bort eller ändra licenstilldelningen i användarens egenskaper. Ändringar måste göras i gruppen och sedan spridas till alla användare.

Det är dock möjligt att tilldela samma produktlicens direkt till användaren, utöver den ärvda licensen. Du kan aktivera ytterligare tjänster från produkten bara för en användare, utan att påverka andra användare.

Direkt tilldelade licenser kan tas bort och påverkar inte ärvda licenser. Tänk på den användare som ärver en Office 365 Enterprise E3-licens från en grupp.

Inledningsvis ärver användaren licensen endast från *E3 grundläggande tjänster* grupp, vilket möjliggör fyra serviceplaner.

1. Välj **Tilldela om** du vill tilldela en E3-licens direkt till användaren. I det här fallet ska du inaktivera alla serviceplaner utom Yammer Enterprise.

    Som ett resultat använder användaren fortfarande bara en licens för E3-produkten. Men den direkta tilldelningen aktiverar yammer Enterprise-tjänsten endast för den användaren. Du kan se vilka tjänster som aktiveras av gruppmedlemskapet jämfört med den direkta tilldelningen.

1. När du använder direkt tilldelning tillåts följande åtgärder:

   - Yammer Enterprise kan stängas av direkt på användarresursen. Växlingsknappen **På/Av** i bilden aktiverades för den här tjänsten, i motsats till de andra tjänstväxlingsknapparna. Eftersom tjänsten är aktiverad direkt på användaren kan den ändras.
   - Ytterligare tjänster kan också aktiveras, som en del av den direkt tilldelade licensen.
   - Knappen **Ta bort** kan användas för att ta bort den direkta licensen från användaren. Du kan se att användaren nu bara har den ärvda grupplicensen och endast de ursprungliga tjänsterna förblir aktiverade:

## <a name="managing-new-services-added-to-products"></a>Hantera nya tjänster som läggs till produkter

När Microsoft lägger till en ny tjänst i en produktlicensplan aktiveras den som standard i alla grupper som du har tilldelat produktlicensen till. Användare i din klientorganisation som prenumererar på meddelanden om produktändringar får e-postmeddelanden i förväg och meddelar dem om de kommande servicetilläggen.

Som administratör kan du granska alla grupper som påverkas av ändringen och vidta åtgärder, till exempel inaktivera den nya tjänsten i varje grupp. Om du till exempel har skapat grupper som endast riktar sig till specifika tjänster för distribution kan du gå tillbaka till dessa grupper och se till att alla nyligen tillagda tjänster är inaktiverade.

Här är ett exempel på hur den här processen kan se ut:

1. Ursprungligen har du tilldelat *Office 365 Enterprise E5-produkten* till flera grupper. En av dessa grupper, som kallas *O365 E5 - Exchange endast* har utformats för att aktivera endast *Exchange Online (Plan 2)* tjänst för sina medlemmar.

2. Du har fått ett meddelande från Microsoft om att E5-produkten kommer att utökas med en ny tjänst - *Microsoft Stream*. När tjänsten blir tillgänglig i din klientorganisation kan du göra följande:

3. Gå till [**Azure Active Directory >-licenser > bladet Alla produkter**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) och välj Office *365 Enterprise E5*och välj sedan **Licensierade grupper** för att visa en lista över alla grupper med den produkten.

4. Klicka på den grupp du vill granska (i det här fallet *O365 E5 - Endast exchange).* Detta öppnar fliken **Licenser.** Om du klickar på E5-licensen öppnas ett blad med alla aktiverade tjänster.
   > [!NOTE]
   > *Microsoft Stream-tjänsten* har lagts till och aktiverats automatiskt i den här gruppen, utöver *Exchange Online-tjänsten:*

   ![Skärmbild av ny tjänst som lagts till i en grupplicens](./media/licensing-group-advanced/manage-new-services.png)

5. Om du vill inaktivera den nya tjänsten i den här gruppen klickar du **på växlingsknappen På/Av** bredvid tjänsten och klickar på knappen **Spara** för att bekräfta ändringen. Azure AD kommer nu att bearbeta alla användare i gruppen för att tillämpa ändringen. Alla nya användare som läggs till i gruppen kommer inte att ha *Microsoft Stream-tjänsten* aktiverad.

   > [!NOTE]
   > Användare kan fortfarande ha tjänsten aktiverad via någon annan licenstilldelning (en annan grupp som de är medlemmar i eller en direkt licenstilldelning).

6. Om det behövs utför du samma steg för andra grupper med den här produkten tilldelad.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Använda PowerShell för att se vem som har ärvt och direktlicenser
Du kan använda ett PowerShell-skript för att kontrollera om användare har en licens som tilldelats direkt eller ärvts från en grupp.

1. Kör `connect-msolservice` cmdleten för att autentisera och ansluta till din klient.

2. `Get-MsolAccountSku`kan användas för att identifiera alla etablerade produktlicenser i klienten.

   ![Skärmbild av Cmdlet Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Använd *värdet AccountSkuId* för den licens du är intresserad av med [det här PowerShell-skriptet](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Detta kommer att producera en lista över användare som har denna licens med information om hur licensen tilldelas.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Använda granskningsloggar för att övervaka gruppbaserad licensieringsaktivitet

Du kan använda [Granskningsloggar](../reports-monitoring/concept-audit-logs.md#audit-logs) för Azure AD för att se all aktivitet som är relaterad till gruppbaserad licensiering, inklusive:
- som har ändrat licenser på grupper
- när systemet började bearbeta en grupplicensändring och när den var klar
- vilka licensändringar har gjorts till en användare som ett resultat av en grupplicenstilldelning.

>[!NOTE]
> Granskningsloggar är tillgängliga på de flesta blad i avsnittet Azure Active Directory i portalen. Beroende på var du kommer åt dem kan filter användas i för hand för att endast visa aktivitet som är relevant för bladets kontext. Om du inte ser de resultat du förväntar dig undersöker du [filtreringsalternativen](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) eller kommer åt de ofiltrerade granskningsloggarna under [**Azure Active Directory > Aktivitet > granskningsloggar**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Ta reda på vem som har ändrat en grupplicens

1. Ange **aktivitetsfiltret** på *Ange grupplicens* och klicka på **Använd**.
2. Resultaten omfattar alla fall av licenser som ställs in eller ändras på grupper.
   >[!TIP]
   > Du kan också skriva namnet på gruppen i filtret *Mål* för att begränsa resultaten.

3. Markera ett objekt i listan om du vill se information om vad som har ändrats. Under *Ändrade egenskaper* visas både gamla och nya värden för licenstilldelningen.

Här är ett exempel på de senaste grupplicensändringarna, med information:

![Ändringar av grupplicenser för skärmbild](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Ta reda på när gruppändringar startade och färdigt bearbetade

När en licens ändras i en grupp börjar Azure AD tillämpa ändringarna på alla användare.

1. Om du vill se när grupper började bearbeta anger du **aktivitetsfiltret** på *Börja tillämpa gruppbaserad licens för användare*. Observera att aktören för åtgärden är *Microsoft Azure AD Group-Based Licensing* - ett systemkonto som används för att köra alla grupplicensändringar.
   >[!TIP]
   > Klicka på ett objekt i listan om du vill visa fältet *Ändrade egenskaper* – det visar de licensändringar som hämtades för bearbetning. Detta är användbart om du har gjort flera ändringar i en grupp och du inte är säker på vilken som har bearbetats.

2. Om du vill se när grupper har bearbetats använder du filtervärdet *Slutför att tillämpa gruppbaserad licens för användare*.
   > [!TIP]
   > I det här fallet innehåller fältet *Ändrade egenskaper* en sammanfattning av resultaten - det är användbart för att snabbt kontrollera om bearbetningen resulterade i några fel. Exempel på utdata:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Om du vill se hela loggen för hur en grupp har bearbetats, inklusive alla användarändringar, anger du följande filter:
   - **Initierad av (aktör):**"Microsoft Azure AD Group-based Licensing"
   - **Datumintervall** (valfritt): anpassat intervall för när du vet att en viss grupp har startat och avslutat bearbetning

Det här exemplet visar början av bearbetningen, alla resulterande användarändringar och färdigbearbetningen.

![Ändringar av grupplicenser för skärmbild](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Om du klickar på objekt som är relaterade till *Ändra användarlicens* visas information om licensändringar som tillämpas på varje enskild användare.

## <a name="deleting-a-group-with-an-assigned-license"></a>Ta bort en grupp med en tilldelad licens

Det går inte att ta bort en grupp med en aktiv licens tilldelad. En administratör kan ta bort en grupp som inte inser att det kommer att orsaka licenser tas bort från användare - av denna anledning kräver vi att alla licenser tas bort från gruppen först, innan den kan tas bort.

När du försöker ta bort en grupp i Azure-portalen kan du se ett felmeddelande som detta: ![Borttagning av skärmgrupper misslyckades](./media/licensing-group-advanced/groupdeletionfailed.png)

Gå till fliken **Licenser** i gruppen och se om det finns några licenser som tilldelats. Om ja tar du bort licenserna och försöker ta bort gruppen igen.

Liknande fel kan visas när du försöker ta bort gruppen via PowerShell eller Graph API. Om du använder en grupp som synkroniserats från lokala, kan Azure AD Connect också rapportera fel om den inte tar bort gruppen i Azure AD. I alla sådana fall, se till att kontrollera om det finns några licenser som tilldelats gruppen, och ta bort dem först.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

Om du använder gruppbaserad licensiering är det en bra idé att bekanta dig med följande lista över begränsningar och kända problem.

- Gruppbaserad licensiering stöder för närvarande inte grupper som innehåller andra grupper (kapslade grupper). Om du använder en licens för en kapslad grupp är det bara den första nivån av användarmedlemmar i gruppen som har licenser.

- Funktionen kan bara användas med säkerhetsgrupper och Office 365-grupper som har securityEnabled=TRUE.

- [Administrationscentret för Microsoft 365](https://admin.microsoft.com) stöder för närvarande inte gruppbaserad licensiering. Om en användare ärver en licens från en grupp visas den här licensen i Office-administratörsportalen som en vanlig användarlicens. Om du försöker ändra licensen eller försöker ta bort licensen returnerar portalen ett felmeddelande. Ärvda grupplicenser kan inte ändras direkt på en användare.

- När licenser tilldelas eller ändras för en stor grupp (till exempel 100 000 användare) kan det påverka prestanda. I synnerhet kan volymen av ändringar som genereras av Azure AD-automatisering negativt påverka prestandan för din katalogsynkronisering mellan Azure AD och lokala system.

- Om du använder dynamiska grupper för att hantera dina användares medlemskap kontrollerar du att användaren är medlem i gruppen, vilket är nödvändigt för licenstilldelning. Om inte [kontrollerar du bearbetningsstatusen för medlemskapsregeln](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) för den dynamiska gruppen.

- I vissa situationer med hög belastning kan det ta lång tid att bearbeta licensändringar för grupper eller medlemskapsändringar i grupper med befintliga licenser. Om du ser dina ändringar tar mer än 24 timmar att bearbeta gruppstorleken på 60 000 användare eller mindre, [öppna en supportbiljett](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) så att vi kan undersöka. 

- Automatisering av licenshantering reagerar inte automatiskt på alla typer av ändringar i miljön. Du kan till exempel ha på licenser, vilket gör att vissa användare är i feltillstånd. Om du vill frigöra tillgängliga platser kan du ta bort vissa direkt tilldelade licenser från andra användare. Systemet reagerar dock inte automatiskt på den här ändringen och åtgärdar användare i det feltillståndet.

  Som en lösning på dessa typer av begränsningar kan du gå till **bladet Grupp** i Azure AD och klicka på **Bearbeta igen**. Det här kommandot bearbetar alla användare i den gruppen och löser feltillstånden om möjligt.

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licenshantering via gruppbaserad licensiering finns här:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Migrera användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
