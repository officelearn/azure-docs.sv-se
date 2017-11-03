---
title: "Hantera enheter med hjälp av Azure portal – preview | Microsoft Docs"
description: "Lär dig hur du använder Azure-portalen för att hantera enheter."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 4b46e1627a229b0649d9ccd2550cd28fda9849f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="managing-devices-using-the-azure-portal---preview"></a>Hantera enheter med hjälp av Azure portal – förhandsgranskning

>[!NOTE]
>Den här funktionen är för närvarande i förhandsversion. Var beredd på att återställa eller ta bort alla ändringar. Funktionen är tillgänglig i alla Azure Active Directory (Azure AD)-prenumeration under förhandsversion. När funktionen blir allmänt tillgänglig, kan vissa aspekter av funktionen kräver en Azure Active Directory premium-prenumeration.


Med hantering av enheter i Azure Active Directory (Azure AD), kan du se till att dina användare har åtkomst till dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. 

Det här avsnittet:

- Förutsätter att du är bekant med den [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)

- Innehåller information om hur du hanterar dina enheter med hjälp av Azure portal


Om du vill hantera enheter i Azure-portalen måste du klicka på **enheter** i den **hantera** avsnitt i den den **Azure Active Directory** bladet.

![Hantera en enhet i Intune](./media/device-management-azure-portal/11.png)




## <a name="configure-device-settings"></a>Konfigurera inställningar för enheter

För att hantera dina enheter med hjälp av Azure portal, behöver de antingen registreras eller anslutna till Azure AD. Du kan finjustera registrera och ansluta enheter genom att konfigurera inställningar för enheter som en administratör.

![Hantera en enhet i Intune](./media/device-management-azure-portal/22.png)


Inställningsbladet enhet kan du konfigurera:

- **Användare kan ansluta enheter till Azure AD** – denna inställningar kan du välja vilka användare som kan ansluta enheter till Azure AD. Standardvärdet är **alla**.

- **Ytterligare lokala administratörer på Azure AD-anslutna enheter** -du kan välja de användare som beviljas lokal administratörsbehörighet på en enhet. Användare som läggs till här läggs till i *Enhetsadministratörer* roll i Azure AD. Globala administratörer i Azure AD och enhetsägare beviljas lokal administratörsbehörighet som standard. Det här alternativet är en premium edition funktioner som är tillgängliga via produkter, till exempel Azure AD Premium eller Enterprise Mobility Suite (EMS). 

- **Användarna kan registrera sina enheter med Azure AD** -måste du konfigurera den här inställningen om du vill att enheter ska registreras med Azure AD. Om du väljer **ingen**, enheter tillåts inte att registrera om de inte Azure AD ansluten eller hybrid anslutna Azure AD. Registrering med Microsoft Intune eller hantering av mobilenheter (MDM) för Office 365 kräver registrering. Om du har konfigurerat någon av dessa tjänster **alla** är markerad och **NONE** är inte tillgänglig...

- **Kräva Multifaktorautentisering ansluta enheter** -du kan välja om användare måste ange en andra autentiseringsfaktor för att ansluta sina enheter till Azure AD. Standardvärdet är **nr**. Vi rekommenderar att kräva multifaktorautentisering vid registrering av en enhet. Innan du aktiverar multifaktorautentisering för den här tjänsten måste du kontrollera att multifaktorautentisering har konfigurerats för de användare som registrerar sina enheter. Mer information om olika Azure Multi-Factor authentication-tjänster finns [komma igång med Azure Multi-Factor authentication](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Högsta antal enheter** -den här inställningen kan du välja det högsta antalet enheter som en användare kan ha i Azure AD. Om användarna når den här kvoten, de är inte lägga till fler enheter förrän ett eller flera av de befintliga enheterna har tagits bort. Enheten citattecken räknas för alla enheter som är Azure AD ansluten eller Azure AD som registrerade idag. Standardvärdet är **20**.

- **Användarna kan synkronisera AppData och inställningar mellan enheter** -som standard är inställningen **NONE**. Välja specifika användare eller grupper eller alla kan användarens inställningar och AppData att synkronisera via sina Windows 10-enheter. Mer information om hur synkroniseringen fungerar i Windows 10.
Det här alternativet är en premium-funktioner som är tillgängliga via produkter, till exempel Azure AD Premium eller Enterprise Mobility Suite (EMS).
 
    ![Hantera en enhet i Intune](./media/device-management-azure-portal/21.png)




## <a name="locate-devices"></a>Hitta enheter

Som en administratör har i Azure-portalen du två alternativ för att hitta registrerade och domänanslutna enheter:

- **Alla enheter** i den **hantera** avsnitt i den **enheter** bladet  

    ![Alla enheter](./media/device-management-azure-portal/41.png)


- **Enheter** i den **hantera** avsnitt i en **användaren** bladet
 
    ![Alla enheter](./media/device-management-azure-portal/43.png)



Med båda alternativen du en vy som:


- Kan du söka efter enheter med visningsnamnet som filter.

- Ger detaljerad översikt över registrerade och domänanslutna enheter

- Du kan utföra vanliga hanteringsuppgifter för enhet
   

![Alla enheter](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Enhetens hanteringsuppgifter

Som administratör kan hantera du registrerade eller anslutna enheter. Det här avsnittet ger information om vanliga hanteringsuppgifter för enheten.


**Hantera en enhet i Intune** -om du är administratör Intune kan du hantera enheter som har markerats som **Microsoft Intune**. En administratör kan se ytterligare enheter 

![Hantera en enhet i Intune](./media/device-management-azure-portal/31.png)


**Aktivera / inaktivera en enhet i Azure AD**

Om du vill aktivera eller inaktivera en enhet, måste vara en global administratör i Azure AD. Inaktivera en enhet förhindrar att en enhet från att komma åt resurserna i Azure AD.  Om du vill inaktivera enheten kan du klicka *...* Klicka på enheten för ytterligare information.

 
![Hantera en enhet i Intune](./media/device-management-azure-portal/33.png)

Inaktivera en enhet ändras tillståndet i den **AKTIVERAD** kolumnen till **nr**.

![Inaktivera en enhet](./media/device-management-azure-portal/32.png)


**Ta bort en Azure AD-enhet** - om du vill ta bort en enhet, du måste vara en global administratör i Azure AD.  
Tar bort en enhet:
 
- Förhindrar att en enhet från att komma åt resurserna i Azure AD 

- Tar bort all information som är kopplade till enheten, till exempel, BitLocker-nycklar för Windows-enheter  

- Representerar en oåterkalleligt aktivitet och rekommenderas inte om det inte krävs

Om en enhet hanteras av en annan hanterare (t.ex. Microsoft Intune), kontrollera att enheten har rensats / dragits tillbaka innan du tar bort enheten i Azure AD.

Du kan antingen välja ”...” ta bort enheten eller klicka på enheten för ytterligare information
 
![Ta bort en enhet](./media/device-management-azure-portal/34.png)


**Visa eller kopiera enhets-ID** -du kan använda enhets-ID för att verifiera ID enhetsinformation på enheten eller med hjälp av PowerShell vid felsökning. Alternativet Kopiera, klicka på enheten.

![Visa en enhets-ID](./media/device-management-azure-portal/35.png)
  

**Visa eller kopiera BitLocker-nycklar** -om du är administratör kan du visa och kopiera BitLocker-nycklar för att hjälpa användare att återställa den krypterade enheten. Nycklarna är bara tillgängliga för Windows-enheter som är krypterade och har sina nycklar lagras i Azure AD. Du kan kopiera dessa nycklar vid åtkomst till information om enheten.
 
![Visa BitLocker-nycklar](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Granskningsloggar


Enheten aktiviteter är tillgängliga via aktivitetsloggarna. Detta omfattar aktiviteter som utlöses av registreringstjänsten för enheten eller användaren:

- Skapa en enhet och lägga till ägare/användare på enheten

- Ändringar av inställningar för enheter

- Enheten åtgärder, till exempel ta bort eller uppdatera en enhet
 
Är din inkörsport till att granska data **granskningsloggar** i den **aktiviteten** avsnitt i den **enheter* bladet.

![Granskningsloggar](./media/device-management-azure-portal/61.png)


En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten

- mål

- initieraren / aktören (som) för en aktivitet

- aktiviteten (vad)

![Granskningsloggar](./media/device-management-azure-portal/63.png)

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.
 
![Granskningsloggar](./media/device-management-azure-portal/64.png)


Om du vill begränsa de data som rapporteras till en nivå som passar dig kan du filtrera granskningsdata med hjälp av följande fält:

- Catergory
- Resurstyp för aktivitet
- Aktivitet
- Datumintervall
- mål
- Initierad av (aktören)

Utöver filter kan du söka efter specifika poster.

![Granskningsloggar](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)



