---
title: Självstudiekurs för massinbjudande B2B-samarbetsanvändare – Azure AD
description: I den här självstudien lär du dig hur du använder PowerShell och en CSV-fil för att skicka massinbjudningar till externa Azure AD B2B-samarbetsanvändare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 2/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c429648adeb0c81799bff2dca1650de965395a60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77166444"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Självstudiekurs: Massinb-inbjudan till Azure AD B2B-samarbetsanvändare (förhandsversion)

|     |
| --- |
| I den här artikeln beskrivs en offentlig förhandsgranskningsfunktion i Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Från och med 2019-12-22 har funktionen Massinbjudna användare (Förhandsversion) tillfälligt inaktiverats.
> Det finns för närvarande inget känt datum för när den här funktionen kommer att återaktiveras i Azure-portalen. Om du vill bjuda in gästanvändare i grupp med PowerShell läser du [självstudiekursen B2B-gruppinb-inb-post](bulk-invite-powershell.md) eller [B2B-koden och PowerShell-exemplen](code-samples.md).

Om du använder Azure Active Directory (Azure AD) B2B-samarbete för att arbeta med externa partners, kan du bjuda in flera gästanvändare till din organisation samtidigt. I den här självstudien får du lära dig hur du använder Azure-portalen för att skicka massinbjudningar till externa användare. Närmare bestämt kan du göra följande:

> [!div class="checklist"]
> * Använd **Massinbjudningsanvändare (förhandsversion)** för att förbereda en kommaavgränsad värdefil (.csv) med användarinformation och inbjudningsinställningar
> * Ladda upp CSV-filen till Azure AD
> * Verifiera att användarna har lagts till i katalogen

Om du inte har Azure Active Directory skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Krav

Du behöver två eller flera test-e-postkonton att skicka inbjudningar till. Kontona måste finnas utanför din organisation. Du kan använda vilken typ av konto som helst, t.ex. konton i sociala medier som gmail.com- eller outlook.com-adresser.

## <a name="invite-guest-users-in-bulk"></a>Bjuda in gästanvändare i grupp

1. Logga in på Azure-portalen med ett konto som är användaradministratör i organisationen.
2. Välj Azure Active **Directory**i navigeringsfönstret .
3. Under **Hantera**väljer du **Användare** > **Massinbjudning**.
4. På sidan **Massinbjudna användare (Förhandsgranska)** väljer du **Hämta** för att hämta en giltig CSV-fil med inbjudningsegenskaper.

    ![Knappen Massinbjudning](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Öppna CSV-filen och lägg till en rad för varje gästanvändare. Obligatoriska värden är:

   * **E-postadress att bjuda in** - den användare som kommer att få en inbjudan

   * **Url för omdirigering** – den URL som den inbjudna användaren vidarebefordras till efter att ha accepterat inbjudan

    ![Exempel på en CSV-fil med gästanvändare inmatade](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Använd inte kommatecken i det **anpassade inbjudningsmeddelandet** eftersom de förhindrar att meddelandet tolkas.

6. Spara filen.
7. På sidan **Massinbjudningsanvändare (Förhandsgranska)** under **Ladda upp csv-filen**bläddrar du till filen. När du markerar filen startar valideringen av CSV-filen. 
8. När filinnehållet har **validerats visas Filen uppladdad**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
9. När filen godkänns väljer du **Skicka** för att starta Azure-massåtgärden som lägger till inbjudningarna. 
10. Om du vill visa jobbstatus väljer **du Klicka här om du vill visa status för varje operation**. Du kan också välja **Massåtgärdsresultat (Förhandsgranska)** i avsnittet **Aktivitet.** Om du vill ha information om varje radartikel i massåtgärden väljer du värdena under kolumnerna **# Lyckades,** **# Fel**eller Totalt **antal begäranden.** Om fel har inträffat visas orsakerna till felet.

    ![Exempel på resultat av massåtgärd](media/tutorial-bulk-invite/bulk-operation-results.png)

11. När jobbet är klart visas ett meddelande om att massåtgärden lyckades.

## <a name="verify-guest-users-in-the-directory"></a>Verifiera gästanvändare i katalogen

Kontrollera att gästanvändarna som du har lagt till finns i katalogen antingen i Azure-portalen eller med PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Visa gästanvändare i Azure-portalen

1. Logga in på Azure-portalen med ett konto som är användaradministratör i organisationen.
2. Välj Azure Active **Directory**i navigeringsfönstret .
3. Under **Hantera** väljer du **Användare**.
4. Under **Visa**väljer du **Endast Gästanvändare** och verifierar att de användare som du har lagt till visas.

### <a name="view-guest-users-with-powershell"></a>Visa gästanvändare med PowerShell

Kör följande kommando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Du bör se de användare som du har bjudit in i listan med ett användarnamn (UPN) i formatet *e-postadress*#EXT#\@*domän*. Till exempel *\@lstokes_fabrikam.com#EXT# contoso.onmicrosoft.com*, där contoso.onmicrosoft.com är den organisation som du skickade inbjudningarna från.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du ta bort testanvändarkontona i katalogen i Azure-portalen på sidan Användare genom att markera kryssrutan bredvid gästanvändaren och sedan välja **Ta bort**. 

Du kan också köra följande PowerShell-kommando för att ta bort ett användarkonto:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Exempel: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skickat massinbjudningar till gästanvändare utanför organisationen. Härnäst ska du få lära dig hur inlösningsprocessen för inbjudningar ser ut.

> [!div class="nextstepaction"]
> [Läs mer om inlösningsprocessen för Azure AD B2B-samarbetsinbjudningar](redemption-experience.md)
