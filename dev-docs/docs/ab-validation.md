# A/B Validation — Architecture Rule

**Prompt:** _"Create element coordinates component."_
**Rule file:** `./rules/architecture.mdc`

---

## Result A — Rule ON

**What was used:** Architecture rule enforced package placement and type constraints. Claude placed coordinate logic in `packages/element/src/coordinates.ts` as a pure TypeScript utility (not a React component), used branded `LocalPoint`/`GlobalPoint` types from `@excalidraw/math`, and called existing `pointFrom()` / `pointTranslate()` utilities instead of re-implementing them.

```ts
// packages/element/src/coordinates.ts
import { pointFrom, pointTranslate } from "@excalidraw/math";
import type { LocalPoint, GlobalPoint } from "@excalidraw/math";
import type { ExcalidrawElement } from "./types";

export function getElementOrigin(element: ExcalidrawElement): GlobalPoint {
  return pointFrom<GlobalPoint>(element.x, element.y);
}

export function getElementCenter(element: ExcalidrawElement): GlobalPoint {
  return pointTranslate<GlobalPoint, GlobalPoint>(
    pointFrom<GlobalPoint>(element.x, element.y),
    [element.width / 2, element.height / 2],
  );
}
```

---

## Result B — Rule OFF

**What was used:** No architectural constraints. Claude created a generic React component with local `useState` and a self-defined `Coordinates` interface, unaware of existing `@excalidraw/math` types or the project's utility functions.

```tsx
// components/ElementCoordinates.tsx
import { useState } from "react";

interface Coordinates {
  x: number;
  y: number;
}

export function ElementCoordinates() {
  const [coords, setCoords] = useState<Coordinates>({ x: 0, y: 0 });

  return (
    <div>
      <span>x: {coords.x}</span>
      <span>y: {coords.y}</span>
    </div>
  );
}
```

---

## Conclusion

The architecture rule produced a result that fits the actual codebase: a pure utility placed in the correct package, using existing branded types and math utilities. Without the rule, the output was a generic React component that duplicates existing abstractions, ignores the project's coordinate type system, and would be placed in the wrong layer. **The rule is effective and worth keeping.**
