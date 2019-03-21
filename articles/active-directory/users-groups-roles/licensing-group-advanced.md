---
title: Gruppbaserad licensiering ytterligare scenarier – Azure Active Directory | Microsoft Docs
description: Fler scenarier för gruppbaserad licensiering i Azure Active Directory
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d2faefd8443383e7afff8e3729bf6f1cf25c3a7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887041"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenarier, begränsningar och kända problem med hjälp av grupper för att hantera licensiering i Azure Active Directory

Använd följande information och exempel för att få en mer avancerade förståelse för gruppbaserad licensiering i Azure Active Directory (AD Azure).

## <a name="usage-location"></a>Användningsplats

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, administratören måste ange den **användningsplats** egenskapen på användaren. I [Azure-portalen](https://portal.azure.com), du kan ange i **användaren** &gt; **profil** &gt; **inställningar**.

För gruppen licenstilldelning ärver alla användare utan att användningsplats angetts platsen för katalogen. Om du har användare på flera platser kan du se till att återspegla som korrekt i dina objekt innan du lägger till användare till grupper med licenser.

> [!NOTE]
> Grupp licenstilldelningen kommer aldrig ändra en befintlig plats för användarvärde för en användare. Vi rekommenderar att du alltid ange användningsplats som en del av ditt användarflöde skapas i Azure AD (t.ex. via AAD Connect-konfiguration) – att säkerställa att resultatet av licenstilldelning alltid är korrekt och användarna får inte tjänster på platser som inte är tillåtna.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Använd gruppbaserad licensiering med dynamiska grupper

Du kan använda gruppbaserad licensiering med någon säkerhetsgrupp, vilket innebär att den kan kombineras med dynamiska grupper i Azure AD. Dynamiska grupper kör regler mot användare objektattribut att automatiskt lägga till och ta bort användare från grupper.

Du kan till exempel skapa en dynamisk grupp för vissa produkter som du vill tilldela till användare. Varje grupp har fyllts i av en regel för att lägga till användare med deras attribut och varje grupp har tilldelats licenser som du vill kunna ta emot. Du kan tilldela attributet lokala platser och synkronisera den med Azure AD, eller du kan hantera attributet direkt i molnet.

Licenser har tilldelats till användaren strax efter att de har lagts till i gruppen. När attributet ändras användaren lämnar grupperna och licenserna tas bort.

### <a name="example"></a>Exempel

Studera exemplet med en lokal lösning för Identitetshantering som avgör vilka användare ska ha åtkomst till Microsofts webbtjänster. Den använder **extensionAttribute1** att lagra ett strängvärde som representerar de licenser som användaren ska ha. Azure AD Connect synkroniserar den med Azure AD.

Användare kanske måste ha en licens men inte en annan, eller båda. Här är ett exempel där du distribuerar Office 365 Enterprise E5 och Enterprise Mobility + Security (EMS)-licenser till användare i grupper:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: grundläggande tjänster

![Skärmbild av Office 365 Enterprise E5 grundläggande tjänster](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licensierade användare

![Skärmbild av Enterprise Mobility + Security licensierade användare](./media/licensing-group-advanced/o365-e5-licensed-users.png)

I det här exemplet ändrar en användare och inställd på värdet av sina extensionAttribute1 `EMS;E5_baseservices;` om du vill att användaren har båda licenserna. Du kan göra den här ändringen på plats. När ändringen synkroniseras med molnet, användaren läggs automatiskt till båda grupperna och licenser har tilldelats.

![Skärmbild som visar hur du ställer in användarens extensionAttribute1](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Var försiktig när du ändrar en befintlig grupp medlemskapsregel. När en regel har ändrats, medlemskap i gruppen kommer att omprövas och användare som matchar den nya regeln inte längre kommer att tas bort (användare som fortfarande matchar den nya regeln inte påverkas under den här processen). Användarna får sina licenser som tas bort under processen vilket kan resultera i förlust av tjänsten eller i vissa fall förlust av data.
> 
> Om du har en stor dynamisk grupp som du lita på för licenstilldelning kan du överväga att verifiera alla omfattande ändringar på en mindre testgrupp innan du tillämpar dem i gruppen huvudsakliga.

## <a name="multiple-groups-and-multiple-licenses"></a>Flera grupper och flera licenser

En användare kan vara medlem i flera grupper med licenser. Här följer några saker att tänka på:

- Flera licenser för samma produkt kan överlappa och de resultera i alla aktiverade tjänster som tillämpas för användaren. I följande exempel visas två licensiering grupper: *E3 bastjänster* innehåller foundation-tjänster för att distribuera först till alla användare. Och *E3 utökade tjänster* innehåller ytterligare tjänster (Sway och Planner) för att distribuera bara till vissa användare. Användaren har lagts till båda grupperna i det här exemplet:

  ![Skärmbild av aktiverade tjänster](./media/licensing-group-advanced/view-enabled-services.png)

  Användaren har därför 7 12 tjänster i produkten aktiverad när du använder bara en licens för den här produkten.

- Att välja den *E3* licens visar mer information, inklusive information om vilka grupper orsakade vilka tjänster som ska aktiveras för användaren.

  ![Skärmbild av aktiverade tjänster efter grupp](./media/licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Direct licenser samexistera med grupplicenserna

När en användare ärver en licens från en grupp, det går inte att direkt ta bort eller ändra denna licenstilldelning i användarens egenskaper. Vara måste gjorda ändringar i gruppen och sedan spridas till alla användare.

Det är dock möjligt att tilldela samma produktlicensen direkt till användaren, förutom den ärvda licensen. Du kan aktivera ytterligare tjänster från produkten för en användare utan att påverka andra användare.

Direkt tilldelade licenser kan tas bort och påverkar inte ärvda licenser. Överväg att den användare som ärver en licens för Office 365 Enterprise E3 från en grupp.

1. Första gången användaren ärver licensen som endast från den *E3 bastjänster* grupp, vilket gör att fyra service-planer som visas:

   ![Skärmbild av E3-gruppen aktiverat tjänster](./media/licensing-group-advanced/e3-group-enabled-services.png)

2. Du kan välja **tilldela** direkt tilldela en E3-licens till användaren. I detta fall använder ska du inaktivera alla service-planer utom Yammer Enterprise:

   ![Skärmbild som visar hur du tilldelar en licens direkt till en användare](./media/licensing-group-advanced/assign-license-to-user.png)

3. Därför kan användaren fortfarande använder bara en licens E3. Men direkttilldelning gör det möjligt för Yammer företagstjänst för den användaren. Du kan se vilka tjänster är aktiverade som gruppmedlemskap kontra direkttilldelning:

   ![Skärmbild av ärvt jämfört med direkt uppgift](./media/licensing-group-advanced/direct-vs-inherited-assignment.png)

4. När du använder direkttilldelning tillåts följande åtgärder:

   - Yammer Enterprise kan stängas av på användarobjektet direkt. Den **på/av** växling i bilden har aktiverats för den här tjänsten, till skillnad från andra knapparna för tjänsten. Eftersom tjänsten är aktiverad direkt på användaren, kan den ändras.
   - Du kan aktivera ytterligare tjänster också som en del av direkt tilldelad licens.
   - Den **ta bort** knappen kan användas för att ta bort direct-licensen från användaren. Du kan se att användaren bara har nu ärvda grupplicens och endast de ursprungliga tjänsterna förbli aktiverat:

     ![Skärmbild som visar hur du tar bort direkt uppgift](./media/licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>Hantera nya tjänster läggs till produkter
När Microsoft lägger till en ny tjänst i en produkt, aktiveras den som standard i alla grupper som du har tilldelat produktlicensen. Användare i din klient som prenumererar på meddelanden om produktförändringar får e-postmeddelanden förväg meddelar dem om kommande-tillägg.

Som administratör kan du granska alla grupper som påverkas av ändringen och vidta åtgärder, till exempel inaktivera den nya tjänsten i varje grupp. Om du har skapat grupper som riktar in sig på endast specifika tjänster för distribution kan du gå tillbaka till dessa grupper och se till att nyligen tillagda är inaktiverade.

Här är ett exempel på hur den här processen kan se ut:

1. Ursprungligen, du har tilldelat den *Office 365 Enterprise E5* produkten till flera grupper. En av dessa grupper kallas *O365 E5 - Exchange endast* har utformats för att aktivera endast de *Exchange Online (Plan 2)* för dess medlemmar.

2. Du har fått ett meddelande från Microsoft som E5 produkten kommer att utökas med en ny tjänst - *Microsoft Stream*. När tjänsten blir tillgänglig i din klientorganisation, kan du göra följande:

3. Gå till den [ **Azure Active Directory > licenser > alla produkter** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) bladet och välj *Office 365 Enterprise E5*och välj sedan **licensierade grupper** att visa en lista över alla grupper med den här produkten.

4. Klicka på den grupp du vill granska (i det här fallet *O365 E5 - Exchange endast*). Då öppnas det **licenser** fliken. När du klickar på licensen som E5 öppnas ett blad som listar alla aktiverade tjänster.
   > [!NOTE]
   > Den *Microsoft Stream* service har lagts till och aktiveras i den här gruppen utöver automatiskt den *Exchange Online* service:

   ![Skärmbild av ny tjänst som lagts till i en grupplicens](./media/licensing-group-advanced/manage-new-services.png)

5. Om du vill inaktivera den nya tjänsten i den här gruppen klickar du på den **på/av** växla bredvid tjänsten och klicka på den **spara** för att bekräfta ändringen. Azure AD kommer nu att bearbeta alla användare i gruppen för att tillämpa ändringen; alla nya användare som lagts till i gruppen har inte den *Microsoft Stream* aktiverad.

   > [!NOTE]
   > Användare kan fortfarande ha tjänsten aktiveras via vissa andra licenstilldelning (en annan grupp som de är medlemmar i eller en direkta licenstilldelningen).

6. Om det behövs, utför du samma steg för andra grupper med den här produkten som tilldelats.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Använd PowerShell för att se vem som har ärvd och direct licenser
Du kan använda ett PowerShell-skript för att kontrollera om användarna har en licens tilldelats direkt eller ärvs från en grupp.

1. Kör den `connect-msolservice` cmdlet för att autentisera och ansluta till din klient.

2. `Get-MsolAccountSku` kan användas för att identifiera alla etablerade produktlicenser i klienten.

   ![Skärmbild av cmdleten Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Använd den *AccountSkuId* värde för den licens som du är intresserad av i med [den här PowerShell.skript](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Detta genererar en lista över användare som har denna licens med information om hur licensen som tilldelas.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Använd granskningsloggarna för att övervaka gruppbaserad licensiering

Du kan använda [Azure AD-granskningsloggar](../reports-monitoring/concept-audit-logs.md#audit-logs) att se alla aktiviteter relaterade till gruppbaserad licensiering, inklusive:
- vem som har ändrat licenser för grupper
- När systemet startar bearbetning av en ändring av licens och när den slutförts
- vilken licensändringar har gjorts till en användare på grund av en grupp licenstilldelning.

>[!NOTE]
> Granskningsloggarna är tillgängliga på de flesta blad i Azure Active Directory-avsnittet i portalen. Beroende på var du åt dem, kanske filter redan används till att bara visa aktivitet som är relevanta för kontexten på bladet. Om du inte ser de resultat du vill ha, granska [filtreringsalternativ](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) eller få åtkomst till de ofiltrerade granskningsloggarna under [ **Azure Active Directory > aktivitet > granskningsloggar** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Ta reda på vem som utförde en grupplicens

1. Ange den **aktivitet** filtrera till *ange grupplicens* och klicka på **tillämpa**.
2. Resultatet innehåller alla fall av licenser ställs in eller ändras på grupper.
   >[!TIP]
   > Du kan också skriva namnet på gruppen i den *Target* filter för att begränsa resultaten.

3. Klicka på ett objekt i listvyn för att se information om vad som har ändrats. Under *ändrade egenskaper* både gamla och nya värdena för licenstilldelningen anges.

Här är ett exempel på gruppen licensändringar, med information:

![Ändringar av skärmbild licens](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Ta reda på när ändringar igång och bearbetats

När en licens ändras i en grupp, startar Azure AD att tillämpa ändringarna för alla användare.

1. Om du vill se när grupper startade bearbetning, ange den **aktivitet** filtrera till *börja tillämpa gruppbaserad licens för användare*. Observera att aktören för åtgärden är *Microsoft Azure AD gruppbaserad licensiering* -ett systemkonto som används för att köra alla ändringar av licens.
   >[!TIP]
   > Klicka på ett objekt i listan för att se den *ändrade egenskaper* field: den visar licensändringar som har hämtas för bearbetning. Detta är användbart om du gjort flera ändringar i en grupp och du inte är säker på vilken som bearbetades.

2. På samma sätt, om du vill se när grupper bearbetats, använda filtervärdet *Slutför tillämpning av gruppbaserad licens för användare*.
   > [!TIP]
   > I det här fallet den *ändrade egenskaper* fältet innehåller en sammanfattning av resultaten – detta är användbart för att snabbt kontrollera om bearbetning resulterade i fel. Exempel på utdata:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Om du vill se en komplett logg för hur en grupp har bearbetats, inklusive alla användarändringar, ange följande filter:
   - **Initierad av (aktör)**: ”Microsoft Azure AD gruppbaserad licensiering”
   - **Datumintervall** (valfritt): anpassade intervall för när du vet att en specifik grupp igång och bearbetats

Detta exempel på utdata visas i början av bearbetning, alla resulterande användarändringar och slutdatumet för bearbetning.

![Ändringar av skärmbild licens](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Artiklar som rör *ändra användarlicens* visas detaljer om licensändringar som tillämpas på varje enskild användare.

## <a name="deleting-a-group-with-an-assigned-license"></a>Tar bort en grupp med en tilldelad licens

Det går inte att ta bort en grupp med en aktiv licens. En administratör kan ta bort en grupp utan att märka att det leder till licenser som ska tas bort från användare – därför kräver vi eventuella licenser som ska tas bort från gruppen först innan den kan tas bort.

När du försöker ta bort en grupp i Azure-portalen kan du se en felmeddelandet Så här: ![Skärmbild bort misslyckades](./media/licensing-group-advanced/groupdeletionfailed.png)

Gå till den **licenser** fliken för gruppen och se om det finns några tilldelade licenser. Om Ja, ta bort dessa licenser och försök att ta bort gruppen igen.

Du kan se liknande fel vid försök att ta bort gruppen från PowerShell eller Graph API. Om du använder en grupp som synkroniseras från den lokala kan Azure AD Connect också fel rapporteras om den inte kan ta bort gruppen i Azure AD. I sådana fall kan se till att kontrollera om det finns alla licenser som tilldelats gruppen och ta bort dem först.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

Om du använder gruppbaserad licensiering, är det en bra idé att bekanta dig med följande lista över begränsningar och kända problem.

- Gruppbaserad licensiering för närvarande stöder inte grupper som innehåller andra grupper (kapslade grupper). Om du använder en licens för en kapslad grupp är det bara den första nivån av användarmedlemmar i gruppen som har licenser.

- Funktionen kan endast användas med säkerhetsgrupper och Office 365-grupper som har securityEnabled = TRUE.

- Den [administrationsportalen för Office 365](https://portal.office.com ) stöder för närvarande inte gruppbaserad licensiering. Om en användare ärver en licens från en grupp, visas denna licens i administrationsportalen för Office som en vanlig användare-licens. Om du försöker ändra den licensen eller försök att ta bort licensen returnerar ett felmeddelande visas i portalen. Ärvda grupplicenserna kan inte ändras direkt på en användare.

- När licenser tilldelas eller ändras för en stor grupp (till exempel 100 000 användare), kan det påverka prestanda. Mer specifikt mängden ändringar som genereras av Azure AD-automation kan ge försämrade prestanda för dina katalogsynkronisering mellan Azure AD och lokala system.

- Om du använder dynamiska grupper för att hantera dina användares medlemskap kontrollerar du att användaren är medlem i gruppen, vilket är nödvändigt för licenstilldelning. Om inte [kontrollerar du bearbetningsstatusen för medlemskapsregeln](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) för den dynamiska gruppen. 

- I vissa situationer med hög belastning, kan det ta lång tid att bearbeta licensändringar för grupper eller Medlemskapsändringar i grupper med befintliga licenser. Om du ser ändringarna ta mer än 24 timmar att bearbeta grupp storleken på 60K användare eller mindre,. [öppna ett supportärende](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) för att vi ska undersöka. 

- Licens management automation reagerar inte automatiskt på alla typer av ändringar i miljön. Exempel: du kan ha slut licenser, orsakar vissa användare ska vara i ett feltillstånd. Att frigöra antal tillgängliga platser du kan ta bort vissa direkt tilldelade licenser från andra användare. Systemet dock inte automatiskt reagera på den här ändringen och rätta till användare i det aktuella tillståndet för fel.

  Som en lösning på dessa typer av begränsningar, kan du gå till den **grupp** bladet i Azure AD, och klicka på **ombearbetning av**. Det här kommandot bearbetar alla användare i gruppen och löser feltillstånd, om möjligt.

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licenshantering via gruppbaserad licensiering finns här:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
