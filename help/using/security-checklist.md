---
title: Die Dispatcher-Sicherheitscheckliste
description: Eine Sicherheitscheckliste, die vor der Produktion geprüft werden sollte.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: ht
source-wordcount: '591'
ht-degree: 100%

---

# Die Dispatcher-Sicherheitscheckliste{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe empfiehlt, vor dem Einsatz in einer Produktionsumgebung die folgende Checkliste abzuarbeiten.

>[!CAUTION]
>
>Gehen Sie vor dem Produktivstart auch die Sicherheitscheckliste für Ihre AEM-Version durch. Siehe die entsprechenden [Adobe Experience Manager-Dokumentation](https://experienceleague.adobe.com/de/docs/experience-manager-65/content/security/security-checklist).

## Verwenden der neuesten Dispatcher-Version {#use-the-latest-version-of-dispatcher}

Installieren Sie die neueste Version, die für Ihre Plattform verfügbar ist. Aktualisieren Sie Ihre Dispatcher-Instanz, um die neueste Version zu verwenden und von Produkt- und Sicherheitsverbesserungen zu profitieren. Weitere Informationen finden Sie unter [Installieren des Dispatchers](dispatcher-install.md).

>[!NOTE]
>
>Die Version Ihrer aktuellen Dispatcher-Installation entnehmen Sie der Dispatcher-Protokolldatei.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Um die Protokolldatei zu finden, überprüfen Sie die Dispatcher-Konfiguration in der Datei `httpd.conf`.

## Einschränken von Clients, die den Cache leeren können {#restrict-clients-that-can-flush-your-cache}

Adobe empfiehlt, [die Clients einzuschränken, die den Cache leeren können](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache).

## Aktivieren von HTTPS für Transport Layer Security {#enable-https-for-transport-layer-security}

Adobe empfiehlt, die HTTPS-Transportschicht in der Autoren- und in der Veröffentlichungsinstanz zu aktivieren. 

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Einschränken des Zugriffs {#restrict-access}

Wenn Sie den Dispatcher konfigurieren, schränken Sie externe Zugriffe so weit wie möglich ein. Siehe das [Beispiel für einen Abschnitt /filter](dispatcher-configuration.md#main-pars_184_1_title) in der Dispatcher-Dokumentation.

## Verweigern des Zugriffs auf administrative URLs  {#make-sure-access-to-administrative-urls-is-denied}

Stellen Sie sicher, dass Sie Filter verwenden, um den externen Zugriff auf sämtliche administrative URLs wie die Web-Konsole zu blockieren.

Unter [Testen der Dispatcher-Sicherheit](dispatcher-configuration.md#testing-dispatcher-security) finden Sie eine Liste der URLs, die blockiert werden müssen.

## Verwenden von Zulassungslisten anstelle von Blockierungslisten {#use-allowlists-instead-of-blocklists}

Zulassungslisten eignen sich besser zur Zugriffskontrolle, da sie grundsätzlich voraussetzen, dass alle Zugriffsanfragen verweigert werden sollten, sofern sie nicht ausdrücklich Teil der Zulassungsliste sind. Dieses Modell ermöglicht eine strengere Kontrolle über neue Anfragen, die während einer bestimmten Konfigurationsphase möglicherweise noch nicht überprüft oder berücksichtigt wurden.

## Ausführen des Dispatchers mit einer dedizierten Systembenutzerin bzw. einem dedizierten Systembenutzer {#run-dispatcher-with-a-dedicated-system-user}

Beim Konfigurieren des Dispatchers sollten Sie sicherstellen, dass der Webserver von einer dedizierten Person mit den geringsten Rechten ausgeführt wird. Es empfiehlt sich, für den Dispatcher-Cache-Ordner nur Schreibrechte zu erteilen.

Außerdem müssen IIS-Benutzende ihre Website folgendermaßen konfigurieren:

1. Wählen Sie in den Einstellungen für den physischen Pfad für Ihre Website die Option zum **Verbinden als bestimmter Benutzer** aus.
1. Legen Sie die Person fest.

## Vermeiden von Denial-of-Service-Angriffen  {#prevent-denial-of-service-dos-attacks}

Ein Denial-of-Service-Angriff (DoS) zielt darauf ab, eine Computerressource für die vorgesehenen Personen unzugänglich zu machen.

Auf Dispatcher-Ebene gibt es [zwei Konfigurationsmöglichkeiten, um DoS-Angriffe zu verhindern](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/configure-aem-dispatcher-to-prevent-dos-attacks-aem-community/m-p/447780).

* Verwenden Sie das mod_rewrite-Modul (beispielsweise [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html), um URL-Überprüfungen durchzuführen (sofern die Regeln für das URL-Muster nicht zu komplex sind).

* Hindern Sie den Dispatcher daran, URLs mit falschen Erweiterungen zwischenzuspeichern, indem Sie [Filter](dispatcher-configuration.md#configuring-access-to-conten-tfilter) verwenden.\
  Ändern Sie beispielsweise die Cachingregeln, um die Zwischenspeicherung auf die erwarteten MIME-Typen zu begrenzen. Dazu zählen unter anderem:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

  Sie können eine Beispielkonfigurationsdatei zum [Einschränken des externen Zugriffs](#restrict-access) einsehen, diese beinhaltet die Einschränkungen für MIME-Typen. 

Um sicher den vollen Funktionsumfang für die Veröffentlichungsinstanzen zu aktivieren, konfigurieren Sie Filter, um den Zugriff auf die folgenden Knoten zu verhindern:

* `/etc/`
* `/libs/`

Konfigurieren Sie anschließend Filter, um den Zugriff auf die folgenden Knotenpfade zuzulassen:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS und JSON)
* `/libs/cq/security/userinfo.json` (CQ-Benutzerinformationen)
* `/libs/granite/security/currentuser.json` (**Daten dürfen nicht zwischengespeichert werden**)

* `/libs/cq/i18n/*` (Internalisierung)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Konfigurieren des Dispatchers zum Verhindern von CSRF-Angriffen {#configure-dispatcher-to-prevent-csrf-attacks}

AEM bietet ein [Framework](https://experienceleague.adobe.com/de/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps), mit dem CSRF-Angriffe (Cross-Site Request Forgery) verhindert werden können. Um dieses Framework ordnungsgemäß zu verwenden, müssen Sie die CSRF-Token-Unterstützung im Dispatcher auf die Zulassungsliste setzen.
<!-- OLD URL ABOVE USED TO BE https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps -->
Gehen Sie hierzu wie folgt vor:

1. Erstellen Sie einen Filter, um den Pfad `/libs/granite/csrf/token.json` zuzulassen.
1. Fügen Sie die Kopfzeile `CSRF-Token` dem Abschnitt `clientheaders` der Dispatcher-Konfiguration hinzu.

## Verhindern von Clickjacking {#prevent-clickjacking}

Um Clickjacking zu verhindern, sollten Sie Ihren Webserver so konfigurieren, dass er den HTTP-Header `X-FRAME-OPTIONS` bereitstellt, der auf `SAMEORIGIN` festgelegt ist.

Weitere Informationen zum Thema Clickjacking finden Sie auf der [OWASP-Website](https://owasp.org/www-community/attacks/Clickjacking).

## Durchführen eines Penetrationstests {#perform-a-penetration-test}

Adobe empfiehlt, Ihre AEM-Infrastruktur vor dem Einsatz in einer Produktionsumgebung einem Penetrationstest zu unterziehen.
