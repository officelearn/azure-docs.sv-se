Om du använder en Azure-prenumeration där du är inte administratör/ägare, till exempel en företagsägd prenumeration, måste du kontrollera följande innan du utför stegen i det här dokumentet:

* Azure inloggningen måste minst ha __deltagaråtkomst__ till Azure-resursgruppen som du använder när du skapar HDInsight (och andra resurser i Azure.)

* En person med minst __deltagaråtkomst__ till Azure-prenumerationen måste ha registrerat leverantören för resursen du använder vid ett tidigare tillfälle. Leverantörregistrering sker när en användare med deltagaråtkomst skapar en resurs för första gången på en prenumeration. Detta kan också inträffa utan att en resurs skapas, om en [leverantör registreras med REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Se följande dokument för mer information om arbete med åtkomst hantering:

* [Kom igång med åtkomsthantering i Azure-portalen](../articles/active-directory/role-based-access-control-what-is.md)
* [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../articles/active-directory/role-based-access-control-configure.md)


<!--HONumber=Sep16_HO5-->


