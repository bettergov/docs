# Embeddable graphics

## Embedding graphics

pym.js

{% hint style="info" %}
Mobile Safari likes to give us grief with pym embeds, often appearing too big for its current frame. To get around this, add these attributes in the parent css:

```css
iframe {
    width: 1px;
    min-width: 100%;
}
```
{% endhint %}

## General style

| Fonts | 15px/1.2 proxima-nova, helvetica, arial, sans-serif \#000 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Hed | bold 18px |
| Deck | regular 15px |
| Notes, credit, source | regular 12px |
| Direct labels | 15px \#000 |
| Graphic meta labels \(e.g. axis\) | 15px \#666 |
| BG grid lines | 1pt \#ddd |
| Outer grid lines \(e.g. axis\) | 1pt \#333 |
| Gray background \(optional\) | \#fafafa |

### Structure

1. Before &lt;div class="bga-g-before"&gt;
   1. Hed
   2. Deck
2. Main &lt;div class="bga-g-main"&gt;
3. After &lt;div class="bga-g-after"&gt;
   1. Notes
   2. Credit
   3. Source

Between divs: 10px spacing \(bottom margin\)

Spot graphics: 250 wide

