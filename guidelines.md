---
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '715'
ht-degree: 57%

---
# Richtlinien für Beiträge zur Adobe Experience Manager-Dokumentation

## Dokumentationsphilosophie

Adobe Experience Manager-Benutzer arbeiten in extrem wettbewerbsorientierten Umgebungen und streben danach, digitale Erlebnisse zu erstellen, die sie von ihren Wettbewerbern abheben. Wenn Adobe fortschrittliche neue Tools in AEM bereitstellt, werden diese durch eine genaue und klare Dokumentation ergänzt, die es den Kunden ermöglicht, ihre AEM sofort zu nutzen und ihren ROI zu maximieren.

Das Ziel der AEM-Dokumentation besteht darin, AEM-Benutzende schnellstmöglich mit der Dokumentation vertraut zu machen. Daher priorisiert Adobe die genaue, nutzbare Dokumentation und bemüht sich, sie laufend zu aktualisieren und zu verbessern.

## Beiträge zur Dokumentation

Zur stetigen Verbesserung der AEM-Dokumentation ist die gesamte Community von AEM-Benutzern herzlich eingeladen, zur Dokumentation beizutragen. Ob durch Pull-Anforderungen oder Probleme, Verbesserungen der Dokumentation können Korrekturen, Klarstellungen, Erweiterungen und andere Beispiele sein.

## Dokumentationsstandards

Während Adobe Beiträge zu seiner Dokumentation begrüßt, sollte jeder Beitrag zur AEM entweder in Form einer Pull-Anforderung oder eines Problems mit den Beitrags- und Dokumentationsstandards von Adobe übereinstimmen.

Beiträge, die diesen Standards nicht entsprechen, können abgelehnt werden.

### Adobe dokumentiert Standardanwendungsfälle.

Die AEM-Dokumentation umfasst Standard-Anwendungsfälle. Anwendungsfälle, die über den Umfang der standardmäßigen Installation und Nutzung des Produkts hinausgehen, sind nicht Teil der AEM-Dokumentation.

### Adobe dokumentiert im Allgemeinen keine Fehler oder ihre Umgehungslösungen.

Die AEM-Dokumentation umfasst Standard-Anwendungsfälle. Aus diesem Grund werden Fehler, durch Fehler verursachte Effekte und Problemumgehungen für Fehler nicht dokumentiert.

Ausnahmen gelten für die Versionshinweise, in denen bekannte Probleme mit möglichen Lösungen aufgelistet werden können, die vom AEM Product Management genehmigt wurden.

### Dokumentationsbeiträge sind nicht zur Beantwortung technischer Fragen geeignet.

Alle Ideen, die Sie zur Verbesserung AEM Dokumentation haben, sind als Beiträge willkommen. Kommentare, Probleme und Pull-Anforderungen sind jedoch als *Beiträge* nur. Sie sollen Ihre Fragen zur Verwendung von AEM, zur Implementierung Ihres AEM oder zur Lösung technischer Probleme nicht beantworten.

Sie können alle Fragen zur AEM oder zu technischen Fehlern melden, indem Sie den normalen Support-Vorgang über die [Experience Cloud Enterprise Support-Portal](https://experienceleague.adobe.com/de?support-solution=General#support) oder in der [Experience Manager-Community](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/ct-p/adobe-experience-manager-community?lang=de).

***AEM Dokumentationsbeiträge sind kein Ersatz für die Adobe-Kundenunterstützung*** und Beiträge, die Antworten auf Supportfragen suchen, werden abgelehnt.

### Die Beiträge müssen sich eindeutig auf die betroffenen Dokumentationsseiten beziehen.

Wenn Sie ein Problem erstellen, um Verbesserungen an der Dokumentation vorzuschlagen, müssen Sie Links zu den betroffenen Seiten angeben. Wenn Sie ein Problem erstellen, indem Sie den Link **Bearbeiten dieser Seite** auf einer Dokumentationsseite verwenden, wird das Problem automatisch mit einem Link zu dieser Seite erstellt.

Dies gilt nicht für Pull-Anforderungen, da Pull-Anforderungen standardmäßig auf die betroffenen Seiten verweisen.

## Dokumentationsrichtlinien

Adobe bittet darum, dass alle Beiträge zu seiner Dokumentation bestimmten Stilrichtlinien entsprechen.

Durch Befolgen dieser Richtlinien wird die Überprüfung Ihres Beitrags vereinfacht. Daher ist die Integration in die Adobe-Dokumentation schneller.

### Sprache und Stil

#### Sprache

* Die AEM-Dokumentation wird in englischer Sprache verfasst und verwaltet.
* Ausdrücke sollten so einfach wie möglich sein.
* Drücken Sie sich klar und bündig aus.

Denken Sie daran, dass die Leserschaft der AEM-Dokumentation international ist und nicht erwartet werden kann, dass alle fließend Englisch beherrschen oder Muttersprachler sind. Vermeiden Sie umgangssprachliche Wendungen und verwenden Sie eine klare und einfache Sprache wie möglich.

#### Befolgen des „Manual of Style von Microsoft®“

Das [Manual of Style von Microsoft®](https://learn.microsoft.com/de-de/style-guide/welcome/) ist ein kostenloses Dokumentationshandbuch, das sich auf Software-Dokumentation konzentriert. Die AEM-Dokumentation folgt diesem Handbuch, soweit möglich.

### Formatierung

| Element | Stil |
|---|---|
| Element oder Option der Benutzeroberfläche | **fett** |
| Dateiname, Pfad, Benutzereingabe, Parameterwerte | `monospaced` |
| Code, Befehlszeile | ```Code Block``` |

### Screenshots

Screenshots sind mit Bedacht und nur dann zu verwenden, wenn eine Textbeschreibung nicht ausreicht.

Markierungen oder andere Anmerkungen in Screenshots (wie rote Rahmen, Pfeile oder Text) sollten nicht verwendet werden. Auf diese Weise können die Screenshots in lokalisierten Versionen der Dokumentation einfacher wiederverwendet oder repliziert werden.

### Versionsspezifische Verweise

Versuchen Sie nach Möglichkeit direkte Verweise auf eine bestimmte Version im gesamten Dokumentationsinhalt zu vermeiden. Dadurch wird die Dokumentation für künftige Versionen flexibler und erweiterbarer.

### Verwendung von Day, AEM, CQ, CRX

Verweisen Sie stets auf das Produkt unter seinem vollständigen Namen. **Adobe Experience Manager** zum ersten Mal in einem Artikel und kann anschließend als **AEM**.

Day, Day-Software, CQ und CRX sollten nur verwendet werden, wenn dies unvermeidlich ist, z. B. in Klassennamen oder bei Verweisen auf die AEM-Historie.