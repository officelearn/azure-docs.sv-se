4. Navigieren Sie zum Gateway-Blatt Ihrer API-App.

    ![Klicken Sie auf "Gateway"](./media/app-service-api-gateway-config-auth/gateway.png)

7. In der **Gateway** Blatt, klicken Sie auf **Einstellungen**, und klicken Sie dann auf **Identität**.

    ![Klicken Sie auf "Einstellungen"](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

    ![Klicken Sie auf "Identität"](./media/app-service-api-gateway-config-auth/clickidentity.png)

    Aus der **Identität** Blatt können Sie zu unterschiedlichen Blättern zum Konfigurieren der Authentifizierung mithilfe von Azure Active Directory und verschiedenen anderen Anbietern navigieren.

    ![Blatt "Identität"](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. Wählen Sie den gewünschten Identitätsanbieter, und führen Sie die Schritte zum Konfigurieren Ihrer API-App für diesen Anbieter aus, die im entsprechenden Artikel aufgeführt sind. Diese Artikel wurden für mobile Apps geschrieben, die Vorgehensweise für API-Apps ist jedoch identisch. Für einige der Verfahren ist das [Azure-Portal] erforderlich. 

 - [Microsoft-Konto](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md)
 - [Facebook-Login](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md)
 - [Twitter-Login](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md)
 - [Google-Login](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)

Die nachfolgenden Screenshots zeigen beispielhaft, welche Seiten im [Azure-Portal] und welche Blätter im [Azure-Vorschauportal] angezeigt werden sollten, nachdem Sie die Azure Active Directory-Authentifizierung eingerichtet haben.

In der Azure-vorschauportal die **Azure Active Directory** Blade verfügt über eine **Client-ID** aus der Anwendung, die Sie in der Registerkarte Azure Active Directory im Azure-Portal erstellt und **zulässige Mandanten** enthält Ihren Azure Active Directory-Mandanten (z. B. "contoso.onmicrosoft.com").

![Blatt "Azure Active Directory"](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

In Azure-Portal die **konfigurieren** Registerkarte für die Anwendung, die Sie erstellt, in haben der **Azure Active Directory** Registerkarte verfügt über die **Anmelde-URL**, **App-ID-URI**, und **-Antwort-URL** aus der **Azure Active Directory** Blatt im Azure-vorschauportal.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(Die Antwort-URL im Screenshot zeigt zweimal dieselbe URL, einmal mit `http:` und einmal mit `https:`.)


