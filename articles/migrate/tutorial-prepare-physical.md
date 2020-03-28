---
title: Förbereda fysiska servrar för utvärdering/migrering med Azure Migrate
description: Lär dig hur du förbereder dig för bedömning/migrering av fysiska servrar med Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5f9048b08b3e77a0c8d5ae9a9d10c614a4e0af61
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336688"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Förbered för bedömning och migrering av fysiska servrar till Azure

I den här artikeln beskrivs hur du förbereder dig för bedömning av lokala fysiska servrar med [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) innehåller ett nav med verktyg som hjälper dig att identifiera, bedöma och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Navet innehåller Azure Migrate-verktyg och ISV-erbjudanden (Independent Software Vendor) från tredje part. 

Den här självstudien är den första i en serie som visar hur du bedömer fysiska servrar med Azure Migrate. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förbered Azure. Ställ in behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbered lokala fysiska servrar för serverutvärdering.


> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan ställa in ett proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i hur du gör för bedömning av fysiska servrar.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prepare-azure"></a>Förbereda Azure

### <a name="azure-permissions"></a>Azure-behörigheter

Du måste ställa in behörigheter för Azure Migrate-distribution.

**Aktivitet** | **Detaljer** 
--- | --- 
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto behöver Contributer- eller ägarbehörigheter för att skapa ett projekt. | 
**Registrera resursleverantörer** | Azure Migrate använder en lätt Azure Migrate-installation för att identifiera och bedöma virtuella hyper-v-datorer med Azure Migrate Server Assessment.<br/><br/> Vid registrering av apparater registreras resursleverantörer med det abonnemang som valts i apparaten. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Om du vill registrera resursleverantörerna behöver du en deltagar- eller ägarroll i prenumerationen.
**Skapa Azure AD-app** | När du registrerar installationen skapar Azure Migrate en Azure Active Directory-app (Azure AD) som används för kommunikation mellan agenter som körs på installationen med sina respektive tjänster som körs på Azure. [Läs mer](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Du behöver behörigheter för att skapa Azure AD-appar (tillgängliga i rollen Programutvecklare).



### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

Kontrollera att du har behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure-portalen och välj **Åtkomstkontroll (IAM)**.
2. Leta reda på det relevanta kontot i **Check access**och klicka på det för att visa behörigheter.
3. Du bör ha **behörigheten Deltagare** eller **Ägare.**
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerationsägare arbetar du med ägaren för att tilldela rollen.


### <a name="assign-permissions-to-register-the-appliance"></a>Tilldela behörigheter för att registrera apparaten

Du kan tilldela behörigheter för Azure Migrate för att skapa Azure AD-appen under registrering av installationer med någon av följande metoder:

- En klient/global administratör kan bevilja behörigheter till användare i klienten för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen Programutvecklare (som har behörigheterna) till kontot.

> [!NOTE]
> - Appen har inga andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
> - Du behöver bara dessa behörigheter när du registrerar en ny apparat. Du kan ta bort behörigheterna när apparaten har konfigurerats.


#### <a name="grant-account-permissions"></a>Bevilja kontobehörigheter

Klienten/den globala administratören kan bevilja behörigheter enligt följande:

1. I Azure AD bör klienten/den globala administratören navigera till**Användarinställningar för** **Azure Active Directory** > **Users** > .
2. Administratören ska ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Det här är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela rollen Programutvecklare

Klienten/den globala administratören kan tilldela rollen Programutvecklare till ett konto. [Läs mer](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Förbered dig för fysisk serverbedömning

För att förbereda för fysisk serverbedömning måste du verifiera de fysiska serverinställningarna och verifiera inställningarna för distribution av installationen:

### <a name="verify-physical-server-settings"></a>Verifiera inställningar för fysiska servrar

1. Verifiera [fysiska serverkrav](migrate-support-matrix-physical.md#physical-server-requirements) för serverbedömning.
2. Kontrollera att de [portar som krävs](migrate-support-matrix-physical.md#port-access) är öppna på fysiska servrar.


### <a name="verify-appliance-settings"></a>Verifiera inställningar för apparat

Innan du konfigurerar Azure Migrate-enheten och börjar utvärderingen i nästa självstudiekurs förbereder du för distribution av enheter.

1. [Kontrollera](migrate-appliance.md#appliance---physical) installationskraven för fysiska servrar.
2. [Granska](migrate-appliance.md#url-access) de Azure-url:er som enheten behöver komma åt.
3. [Granska](migrate-appliance.md#collected-data---vmware) att apparaten kommer att samla in under upptäckt och bedömning.
4. [Bedömning](migrate-support-matrix-physical.md#port-access) av åtkomstkrav för portar.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Konfigurera ett konto för identifiering av fysiska servrar

Azure Migrate behöver behörigheter för att identifiera lokala servrar.

- **Windows:** Konfigurera ett lokalt användarkonto på alla Windows-servrar som du vill inkludera i identifieringen. Användarkontot måste läggas till i följande grupper: - Användare av fjärrhantering - Användare av prestandaövervakare - Prestandalogganvändare
- **Linux:** Du behöver ett rotkonto på De Linux-servrar som du vill upptäcka.

## <a name="prepare-for-physical-server-migration"></a>Förbered för fysisk servermigrering

Granska kraven för migrering av fysiska servrar.

- [Granska](migrate-support-matrix-physical-migration.md#physical-server-requirements) fysiska serverkrav för migrering.
- Azure Migrate: Servermigrering använder en replikeringsserver för fysisk servermigrering:
    - [Granska](migrate-replication-appliance.md#appliance-requirements) distributionskraven för replikeringsverktyget och [alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på enheten.
    - Granska [åtkomstkraven](migrate-replication-appliance.md#url-access) för URL och [port] (migrera-replikering-appliance.md#port-access) för replikeringsverktyget.


## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera Azure-kontobehörigheter.
> * Förberedde fysiska servrar för bedömning.

Fortsätt till nästa självstudie för att skapa ett Azure Migrate-projekt och utvärdera fysiska servrar för migrering till Azure

> [!div class="nextstepaction"]
> [Utvärdera fysiska servrar](./tutorial-assess-physical.md)
