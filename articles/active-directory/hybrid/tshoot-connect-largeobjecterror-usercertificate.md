---
title: Azure AD Connect – LargeObject-fel som orsakas av attributet userCertificate | Microsoft-dokument
description: Det här avsnittet innehåller reparationsstegen för LargeObject-fel som orsakas av attributet userCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "62095497"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect-synkronisering: Hantera LargeObject-fel som orsakas av attributet userCertificate

Azure AD tillämpar en maximal gräns på **15** certifikatvärden för **attributet userCertificate.** Om Azure AD Connect exporterar ett objekt med fler än 15 värden till Azure AD returnerar Azure AD ett **LargeObject-fel** med meddelande:

>*– Det avsättningarda objektet är för stort. Trimma antalet attributvärden för det här objektet. Åtgärden kommer att göras om i nästa synkroniseringscykel..."*

LargeObject-felet kan orsakas av andra AD-attribut. För att bekräfta att det verkligen orsakas av attributet userCertificate måste du verifiera mot objektet antingen i lokalt AD eller i [Metaversverse-sökning i Synkroniseringstjänsthanteraren](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Om du vill hämta listan över objekt i din klient med LargeObject-fel använder du någon av följande metoder:

 * Om din klient är aktiverad för Azure AD Connect Health för synkronisering kan du referera till [den synkroniseringsfelrapport](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync) som tillhandahålls.
 
 * E-postmeddelandet för katalogsynkroniseringsfel som skickas i slutet av varje synkroniseringscykel har listan över objekt med LargeObject-fel. 
 * På [fliken Synkroniseringstjänsthanterarens verksamhet](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) visas listan över objekt med LargeObject-fel om du klickar på den senaste åtgärden Exportera till Azure AD.
 
## <a name="mitigation-options"></a>Begränsningsalternativ
Tills LargeObject-felet har lösts kan inte andra attributändringar till samma objekt exporteras till Azure AD. För att lösa felet kan du överväga följande alternativ:

 * Uppgradera Azure AD Connect för att bygga 1.1.524.0 eller efter. I Azure AD Connect build 1.1.524.0 har synkroniseringsreglerna för out-of-box uppdaterats för att inte exportera attribut userCertificate och userSMIMECertificate om attributen har fler än 15 värden. Mer information om hur du uppgraderar Azure AD Connect finns i artikeln [Azure AD Connect: Upgrade from a previous version to the latest](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementera en **utgående synkroniseringsregel** i Azure AD Connect som exporterar ett **null-värde i stället för de faktiska värdena för objekt med fler än 15 certifikatvärden**. Det här alternativet är lämpligt om du inte kräver att något av certifikatvärdena exporteras till Azure AD för objekt med fler än 15 värden. Mer information om hur du implementerar den här synkroniseringsregeln finns i nästa avsnitt [Implementera synkroniseringsregel för att begränsa export av attributet userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Minska antalet certifikatvärden i det lokala AD-objektet (högst 15) genom att ta bort värden som inte längre används av din organisation. Detta är lämpligt om attributet svälla orsakas av utgångna eller oanvända certifikat. Du kan använda [PowerShell-skriptet som finns tillgängligt här](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) för att hitta, säkerhetskopiera och ta bort utgångna certifikat i din lokala AD. Innan du tar bort certifikaten rekommenderar vi att du verifierar med administratörerna för offentlig nyckel-infrastruktur i organisationen.

 * Konfigurera Azure AD Connect för att utesluta attributet userCertificate från att exporteras till Azure AD. I allmänhet rekommenderar vi inte det här alternativet eftersom attributet kan användas av Microsoft Online Services för att aktivera specifika scenarier. Framför allt:
    * Attributet userCertificate på user-objektet används av Exchange Online- och Outlook-klienter för meddelandesignering och kryptering. Mer information om den här funktionen finns i artikel [S/MIME för meddelandesignering och kryptering](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Attributet userCertificate på datorobjektet används av Azure AD för att tillåta windows 10 lokala domänanslutna enheter att ansluta till Azure AD. Mer information om den här funktionen finns i artikeln [Anslut domänanslutna enheter till Azure AD för Windows 10-upplevelser](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementera synkroniseringsregel för att begränsa export av userCertificate-attribut
Om du vill lösa largeobject-felet som orsakas av attributet userCertificate kan du implementera en utgående synkroniseringsregel i Azure AD Connect som exporterar ett **null-värde i stället för de faktiska värdena för objekt med mer än 15 certifikatvärden**. I det här avsnittet beskrivs de steg som krävs för att implementera synkroniseringsregeln för **användarobjekt.** Stegen kan anpassas för **kontakt-** och **datorobjekt.**

> [!IMPORTANT]
> Om du exporterar null-värde tas certifikatvärden som tidigare exporterats till Azure AD bort.

Stegen kan sammanfattas som:

1. Inaktivera synkroniseringsschemaläggare och kontrollera att ingen synkronisering pågår.
3. Hitta den befintliga utgående synkroniseringsregeln för attributet userCertificate.
4. Skapa den utgående synkroniseringsregeln som krävs.
5. Verifiera den nya synkroniseringsregeln för ett befintligt objekt med LargeObject-fel.
6. Använd den nya synkroniseringsregeln på återstående objekt med LargeObject-fel.
7. Kontrollera att det inte finns några oväntade ändringar som väntar på att exporteras till Azure AD.
8. Exportera ändringarna till Azure AD.
9. Återaktivera synkroniseringsschemat.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1. Inaktivera synkroniseringsschemaläggare och kontrollera att ingen synkronisering pågår
Se till att ingen synkronisering sker medan du är mitt i implementeringen av en ny synkroniseringsregel för att undvika att oavsiktliga ändringar exporteras till Azure AD. Så här inaktiverar du den inbyggda synkroniseringsschemat:
1. Starta PowerShell-sessionen på Azure AD Connect-servern.

2. Inaktivera schemalagd synkronisering genom att köra cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Föregående steg gäller endast för nyare versioner (1.1.xxx.x) av Azure AD Connect med den inbyggda schemaläggaren. Om du använder äldre versioner (1.0.xxx.x) av Azure AD Connect som använder Windows Schemaläggaren, eller om du använder din egen anpassade schemaläggare (inte vanlig) för att utlösa periodisk synkronisering, måste du inaktivera dem därefter.

1. Starta **synkroniseringstjänsthanteraren** genom att gå till START → Synkroniseringstjänsten.

1. Gå till fliken **Operationer** och bekräfta att det inte finns någon åtgärd vars status är *"pågår".*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Steg 2. Hitta den befintliga utgående synkroniseringsregeln för attributet userCertificate
Det bör finnas en befintlig synkroniseringsregel som är aktiverad och konfigurerad för att exportera userCertificate-attribut för användarobjekt till Azure AD. Leta reda på den här synkroniseringsregeln för att ta reda på dess **prioritet** och **omfångsfilterkonfiguration:**

1. Starta **redigeraren** för synkroniseringsregler genom att gå till START → Redigeraren för synkroniseringsregler.

2. Konfigurera sökfiltren med följande värden:

    | Attribut | Värde |
    | --- | --- |
    | Riktning |**Utgående** |
    | MV-objekttyp |**Person** |
    | Anslutningsprogram |*namnet på din Azure AD-anslutning* |
    | Objekttyp för koppling |**Användaren** |
    | MV-attribut |**userCertificate** |

3. Om du använder OOB-synkroniseringsregler (out-of-box) till Azure AD-anslutningsapp för att exportera attributet UserCertficiate för användarobjekt, bör du få tillbaka regeln *"Ut till AAD – User ExchangeOnline".*
4. Anteckna **prioritetsvärdet för** den här synkroniseringsregeln.
5. Markera synkroniseringsregeln och klicka på **Redigera**.
6. Klicka på **Nej**i popup-dialogrutan *"Redigera reserverad regelbekräftelse"* . (Oroa dig inte, vi kommer inte att göra några ändringar i denna synkroniseringsregel).
7. Välj fliken **Scoping-filter på redigeringsskärmen.**
8. Anteckna omfångsfilterkonfigurationen. Om du använder OOB-synkroniseringsregeln bör det finnas **exakt en filtergrupp för omfång som innehåller två satser,** inklusive:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | Lika | Användare |
    | cloudMastered | NOTER | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Steg 3. Skapa den utgående synkroniseringsregel som krävs
Den nya synkroniseringsregeln måste ha samma **omfångsfilter** och **högre prioritet** än den befintliga synkroniseringsregeln. Detta säkerställer att den nya synkroniseringsregeln gäller för samma uppsättning objekt som den befintliga synkroniseringsregeln och åsidosätter den befintliga synkroniseringsregeln för attributet userCertificate. Så här skapar du synkroniseringsregeln:
1. Klicka på knappen **Lägg till ny regel** i redigeraren för synkroniseringsregler.
2. Ange följande konfiguration under **fliken Beskrivning:**

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | T.ex. *"Ut till AAD – Anpassad åsidosättning för userCertificate"* |
    | Beskrivning | *Ge en beskrivning* | T.ex. *"Om attributet userCertificate har fler än 15 värden exporterar du NULL."* |
    | Anslutet system | *Välj Azure AD-anslutning* |
    | Ansluten systemobjekttyp | **Användaren** | |
    | Objekttyp för metaversum | **Person** | |
    | Länktyp | **Anslut** | |
    | Prioritet | *Välj ett nummer mellan 1 - 99* | Det valda talet får inte användas av någon befintlig synkroniseringsregel och har ett lägre värde (och därmed högre prioritet) än den befintliga synkroniseringsregeln. |

3. Gå till fliken **Omfångsfilter** och implementera samma omfångsfilter som den befintliga synkroniseringsregeln använder.
4. Hoppa över fliken Gå med i **regler.**
5. Gå till fliken **Omvandlingar** om du vill lägga till en ny omvandling med följande konfiguration:

    | Attribut | Värde |
    | --- | --- |
    | Flödestyp |**Uttryck** |
    | Målattribut |**userCertificate** |
    | Källattribut |*Använd följande uttryck:*`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klicka på knappen **Lägg** till om du vill skapa synkroniseringsregeln.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Steg 4. Verifiera den nya synkroniseringsregeln för ett befintligt objekt med LargeObject-fel
Detta för att kontrollera att synkroniseringsregeln som skapas fungerar korrekt på ett befintligt AD-objekt med LargeObject-fel innan du tillämpar den på andra objekt:
1. Gå till fliken **Operationer** i Synkroniseringstjänsthanteraren.
2. Välj den senaste åtgärden Exportera till Azure AD och klicka på ett av objekten med LargeObject-fel.
3.  Klicka på knappen **Förhandsgranska** på popup-skärmen Objektegenskaper för kopplingsutrymme.
4. På popup-skärmen Förhandsgranska väljer du **Fullständig synkronisering** och klickar på **Förhandsgranska förhandsgranskning**.
5. Stäng skärmen Förhandsgranska och skärmen Egenskaper för objektegenskaper för anslutningsutrymme.
6. Gå till fliken **Kontakter** i Synkroniseringstjänsthanteraren.
7. Högerklicka på **Azure AD** Connector och välj **Kör...**
8. I popup-programmet Kör koppling väljer du **Exportera** steg och klickar på **OK**.
9. Vänta tills Export till Azure AD har slutförts och bekräfta att det inte finns något fler LargeObject-fel på det här specifika objektet.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Steg 5. Tillämpa den nya synkroniseringsregeln på återstående objekt med LargeObject-fel
När synkroniseringsregeln har lagts till måste du köra ett fullständigt synkroniseringssteg på AD Connector:
1. Gå till fliken **Kontakter** i Synkroniseringstjänsthanteraren.
2. Högerklicka på **AD-kopplingen** och välj **Kör...**
3. I popup-programmet Kör koppling väljer du **Hela synkroniseringssteget** och klickar på **OK**.
4. Vänta tills steget Fullständig synkronisering har slutförts.
5. Upprepa ovanstående steg för de återstående AD-kopplingarna om du har fler än en AD-koppling. Vanligtvis krävs flera kopplingar om du har flera lokala kataloger.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Steg 6. Kontrollera att det inte finns några oväntade ändringar som väntar på att exporteras till Azure AD
1. Gå till fliken **Kontakter** i Synkroniseringstjänsthanteraren.
2. Högerklicka på **Azure AD** Connector och välj **Sök connectorutrymme**.
3. I popup-programmet Sök connector space:
    1. Ange scope till **väntande export**.
    2. Markera alla tre kryssrutor, inklusive **Lägg till,** **Ändra** och **ta bort**.
    3. Klicka på **Sök-knappen** om du vill returnera alla objekt med ändringar som väntar på att exporteras till Azure AD.
    4. Kontrollera att det inte finns några oväntade ändringar. Om du vill undersöka ändringarna för ett visst objekt dubbelklickar du på objektet.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Steg 7. Exportera ändringarna till Azure AD
Så här exporterar du ändringarna till Azure AD:
1. Gå till fliken **Kontakter** i Synkroniseringstjänsthanteraren.
2. Högerklicka på **Azure AD** Connector och välj **Kör...**
4. I popup-programmet Kör koppling väljer du **Exportera** steg och klickar på **OK**.
5. Vänta tills Export till Azure AD har slutförts och bekräfta att det inte finns några fler LargeObject-fel.

### <a name="step-8-re-enable-sync-scheduler"></a>Steg 8. Återaktivera synkroniseringsschemaläggare
Nu när problemet är löst aktiverar du den inbyggda synkroniseringsschemat igen:
1. Starta PowerShell-sessionen.
2. Återaktivera schemalagd synkronisering genom att köra cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Föregående steg gäller endast för nyare versioner (1.1.xxx.x) av Azure AD Connect med den inbyggda schemaläggaren. Om du använder äldre versioner (1.0.xxx.x) av Azure AD Connect som använder Windows Schemaläggaren, eller om du använder din egen anpassade schemaläggare (inte vanlig) för att utlösa periodisk synkronisering, måste du inaktivera dem därefter.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

