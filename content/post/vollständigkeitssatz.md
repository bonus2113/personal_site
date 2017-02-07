{
  "date": "2016-09-22T10:54:00",
  "draft": false,
  "image": "",
  "math": true,
  "notonhomepage": true,
  "subtitle": "Too much Math",
  "tags": [
    "math"
  ],
  "title": "Vollständigkeitssatz für den Sequenzenkalkül in der Prädikatenlogik",
  "video": ""
}

Note: This is something I wrote down while studying for my mathematical logic exam

Zu zeigen ist, dass alle gültigen Sequenzen im Sequenzkalkül herleitbar sind. Die Beweis Idee ist wie folgt:   
Wir wissen schon, dass alle Sequenzen die herleitbar sind auch korrekt sind (Korrektheitssatz). Wenn wir zeigen, dass alle *nicht* herleitbaren Sequenzen *nicht* korrekt sind, dann kann es keine korrekte Sequenz geben, die nicht herleitbar ist.

Wenn das bewiesen ist, wissen wir:  
- Herleitbar ⇒ Korrekt  
- Nicht Herleitbar ⇒ Nicht Korrekt = (Kontraposition) Korrekt ⇒ Herleitbar  

Also auch:
- Herleitbar gdw. Korrekt

Um diesen Beweis zu führen brauch man folgende Annahme:

1. Wenn ein Satz ψ aus einer Satzmenge Φ herleitbar ist, dann folgt ψ aus Φ semantisch.  

Für den Beweis nehmen wir also an, dass ψ nicht herleitbar ist und zeigen, dass dann ψ nicht aus Φ folgen kann.

*Was bedeutet "herleitbar" und "folgen"?*

"ψ herleitbar aus Φ" heißt, dass die Sequenz "Γ, Γ' ⇒ ψ" mit Γ <= Φ gültig ist.  
"ψ folgt aus Φ" heißt, dass jedes Modell von Φ auch ein Modell von ψ ist.  

Insbesondere ist Φ u { ¬ψ } nicht erfüllbar, wenn ψ aus Φ folgt. Das ist leicht einzusehen. Ein Modell von ψ ist *kein* Modell von ¬ψ. Wenn jedes Modell von Φ auch ein Modell von ψ ist, dann ist auch jedes Modell von Φ *kein* Modell von ¬ψ. Also erfüllt jede Interpretation entweder Φ oder ¬ψ, aber nie beide und Φ u { ¬ψ } ist nicht erfüllbar.

Also, wenn wir zeigen wollen, dass ψ nicht aus Φ folgt müssen wir nur ein Modell von Φ u {¬ψ} finden.

In dem Beweis wird zu jeder *nicht aus Φ herleitbaren* Sequenz Γ ⇒ ∆ ein Modell von Γ u Φ u ¬∆ konstruiert und damit gezeigt, dass dann jedes ψ ∈ ∆ nicht aus Φ folgen kann.

Der Rest des Beweises beschäftigt sich mit der Konstruktion dieses Modelles.

Dies folgt in zwei Schritten:

*Herbrandstrukturen*

Es wird zu Mengen Σ von *atomaren* Sätzen ein Modell konstruiert. Diese Struktur heißt "Herbrandtstruktur über Σ". Dabei tritt die Komplikation auf, dass "Gleichheiten herausfaktorisiert" werden müssen. Es ist nämlich so, dass für zwei syntaktisch unterschiedliche Terme wie z.B. "fc" und "ffc" trotzdem Gleichheit gelten kann wenn in Σ der Satz "fc = ffc" vorkommt. In einer normalen Herbrandtstruktur, die nur die *Signatur* beachtet wäre fc != ffc weil es unterschiedliche Terme sind und damit die Gleichheit nicht erfüllt und die Struktur kein Modell von Σ. Um dieses Problem zu lösen wird das Konzept von *Kongurenzklassen* und *Kongruenzrelationen* eingeführt. Diese sind nur dafür da um auszudrücken, dass für zwei syntaktisch unterschiedliche Terme trotzdem Gleichheit gelten kann.  
Das zweite Problem was eintritt ist, dass in Σ Gleichheiten impliziert werden können, die aber nicht über einzelne Sätze formuliert werden. Wenn z.B. die Sätze "a=b" und "b=c" in Σ vorkommen, dann muss für jedes Modell von Σ auch gelten "a=c". Die Herbrandtstruktur über Σ kann dies aber nicht sehen, da die Kontruktion rein *syntaktisch* ist. Anstatt die Kontruktion des Modells komplizierter zu machen werden weitere Anforderungen an Σ gestellt. Σ muss "abgeschlossen unter Substitution sein. Das heißt in dem Beispiel, dass wenn Σ "a=b" und "b=c" enthält auch "a=c" in Σ sein muss. Dies muss für alle Sätz in Σ gelten. So zum Beispiel auch wenn "a=b", "fb=c" ∈ Σ auch "fa=c" ∈ Σ. 

Nach dem viele Rumbauen hat man schließlich für jedes *unter Substitution abgeschlossene* Σ eine Struktur A, für die gilt A |= ψ gdw. ψ ∈ Σ. 

Wir erinnern uns hier, dass Σ eine Menge *atomarer* Sätze ist. Wir suchen aber ein Modell von einer Menge Γ u Φ u ¬∆ die auch nicht¬atomare Sätze enthalten kann. Glücklicherweise dürfen wir in dem Beweis Satzmengen *erweitern*, d.h. mehr Sätze dazu nehmen um bestimmte Abschlusseigenschaften zu erfüllen. Wenn wir zeigen können, dass die erweiterte Menge eine Modell hat, dann hat jede Teilmenge, also auch die, die uns interessiert, ein Modell. Beim dazu nehmen von Sätzen können wir nämlich nur Modelle streichen, aber nie welche dazu bekommen.

*Hintikka-Mengen*

Die gesuchte Erweiterungen von Γ u Φ u ¬∆ heißen "Hintikka-Menge". Der zweite Teil des Beweises beschäftigt sich nur damit, dass aus der Annahme (Die Sequenz Γ ⇒ ∆ ist nicht aus Φ ableitbar) folgt, dass eine passende Erweiterung von Γ u Φ u ¬∆ existiert. Wenn diese Erweiterung existiert, dann können wir über die Konstruktion im vorherigen Teil ein Modell von Γ u Φ u ¬∆ konstruieren und damit unser Ziel erreichen. 

Der Beweis erfolgt in drei Schritten:

1. Es wird gezeigt, dass alle *nicht aus Φ herleitbaren* Sequenzen Γ\* ⇒ ∆\* aufzählbar sind.
2. Es werden bestimmte Eigenschaften für alle Γ und ∆ aus dem vorherigen Schritt gezeigt. Insbesondere ist wichtig, dass Φ <= Γ\* gilt, da man jetzt nur noch ein Modell von Γ\* u ¬∆\* finden muss.
3. Als letztes wird gezeigt, dass wenn man Σ = Menge aller atome in Γ\* u ¬∆\* nimmt und daraus die im vorherigen Abschnitt dargestellt Kontruktion anwendet die resultierende Struktur auch ein Modell von Γ\* u ¬∆\* ist.

Jetzt haben wir ein Modell von Γ\* u ¬∆\* und damit auch von Γ u Φ u ¬∆. Das ist das Modell was wir am Anfang finden wollten und damit sind wir fertig.
