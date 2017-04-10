Du kan använda en Azure-prenumeration som du inte är administratör för eller ägare till, till exempel en företagsägd prenumeration. I så fall måste du kontrollera att följande har erhållits för att kunna följa anvisningarna i den här artikeln:

* Deltagaråtkomst. Om du vill logga in i Azure måste du ha minst deltagaråtkomst till Azure-resursgruppen. Den här resursgruppen används för att skapa ett HDInsight-kluster och andra Azure-resurser.
* Leverantörregistrering. En person med minst deltagaråtkomst till Azure-prenumerationen måste ha registrerat providern för resursen du använder vid ett tidigare tillfälle. Leverantörregistrering sker när en användare med deltagaråtkomst skapar en resurs för första gången på en prenumeration. Detta kan också inträffa utan att en resurs skapas, om en [leverantör registreras via REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Se följande artiklar för mer information om arbete med åtkomsthantering:

* [Kom igång med åtkomsthantering i Azure-portalen](../articles/active-directory/role-based-access-control-what-is.md)
* [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../articles/active-directory/role-based-access-control-configure.md)
