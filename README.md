# Seasonal Restaurant Menu Project

A responsive restaurant menu built with semantic HTML and modern CSS. The project uses an accessible structure, BEM naming, Flexbox layouts, fluid typography, and carefully adjusted color contrast to present a seasonal menu across desktop and mobile screen sizes.

**Designed and developed by Marco Bolden**

**Project type:** Independent HTML and CSS learning project

**Status:** In development

## Project Goals

- Build a responsive seasonal restaurant menu
- Use semantic HTML to create a clear document structure
- Improve accessibility through meaningful labels and readable content
- Organize class names using the BEM naming convention
- Use Flexbox to create adaptable page and card layouts
- Display menu cards in two columns on larger screens and stack them on smaller screens
- Create a clear visual and typographic hierarchy
- Maintain readable color contrast throughout the design
- Make navigation links comfortable to use on touch screens
- Keep the CSS organized and easy to maintain

---

## Built With

- Semantic HTML
- Modern CSS
- Flexbox
- CSS custom properties
- BEM naming convention
- Fluid typography with `clamp()` and `calc()`
- Media queries
- Relative units
- Accessible color contrast
- Responsive, mobile-first styling

---

## Challenges and Solutions

### Creating the Two-Column Menu Layout

The biggest challenge in this project was arranging the four menu cards into a two-by-two layout on larger screens.

I unintentionally began with a mobile-first layout because I worked with the code editor on one side of the screen and the Live Server preview on the other.

At that narrower preview width, displaying each card in a single column worked well. However, when I viewed the page across the full screen, the cards remained in one long column. The layout was usable, but it required considerably more scrolling and did not match the design I had envisioned.

CSS Grid would have been a natural choice for a two-dimensional layout, but one of the goals of this project was to gain more experience with Flexbox. I therefore kept the card container as a wrapping flex container:

```css
.menu__cards {
  display: flex;
  flex-wrap: wrap;
  gap: var(--layout-gap);
}
```

The mobile layout gives every card a full row:

```css
.menu-card {
  flex: 0 0 100%;
}
```

At the selected breakpoint, I changed only the cards’ `flex-basis`:

```css
@media (min-width: 48rem) {
  .menu-card {
    flex-basis: calc((100% - var(--layout-gap)) / 2);
  }
}
```

The calculation takes the available container width, subtracts the gap between the two cards, and divides the remaining space equally:

```plaintext
(container width − one gap) ÷ two cards
```

This produces exactly two cards per row without changing the rest of the Flexbox configuration. Below the breakpoint, the original `100%` basis remains in effect, preserving the single-column mobile layout.

The `48rem` breakpoint initially felt like a magic number. Through testing, I learned that a breakpoint should be selected according to where the content and layout need to change, rather than according to a particular device. I also gained a much clearer understanding of `flex-basis`. It establishes the starting size of a flex item along the main axis before Flexbox distributes the available space.

Although Grid may have required less code for this particular layout, solving it with Flexbox helped me understand wrapping, gaps, responsive breakpoints, and calculated flex bases. It also showed me that a focused change to one property can sometimes solve a layout problem more cleanly than adding several new rules.

### Using Logical Properties

This project uses logical CSS properties such as `margin-inline`, `margin-block-start`, `margin-block-end`, `padding-block`, and `border-block-end` instead of relying exclusively on physical properties such as `margin-left`, `margin-bottom`, `padding-top`, and `border-bottom`.

Logical properties describe spacing and borders according to the flow of the document:

- `inline` represents the direction in which text flows.
- `block` represents the direction in which elements are stacked.
- `start` and `end` describe positions along those axes.

For example, I used the following rule to center the main page containers:

```css
.site-header,
.menu,
.site-footer {
  width: min(100% - 2rem, 72rem);
  margin-inline: auto;
}
```

On this English-language page, `margin-inline: auto` produces the same visible result as setting both `margin-left` and `margin-right` to `auto`.

However, it describes the intention more clearly: apply automatic margins along the inline axis.

I used `margin-block-start` and `margin-block-end` for vertical spacing:

```css
.menu__information {
  margin-block-start: var(--layout-gap);
}

.menu-intro {
  margin-block-end: 2.5rem;
}
```

In the current writing direction, these create space above and below the elements. Unlike physical properties, logical properties can also adapt to different writing directions and writing modes.

For spacing between Flexbox children, I used `gap` instead of adding margins to individual items:

```css
.menu__cards {
  display: flex;
  flex-wrap: wrap;
  gap: var(--layout-gap);
}
```

`gap` places space only between the Flexbox items. It does not add unwanted space before the first item or after the last item, and it continues to work cleanly when the cards wrap.

This project contains substantially more CSS than my first independent project. As the stylesheet grew, choosing consistent property patterns became more important. Logical properties helped me reason about spacing by axis and document flow instead of treating every side as an unrelated value.

The central explanation is:

> I selected the property according to the relationship I wanted to control.

For example:

| Intended relationship            | Property choice       |
| -------------------------------- | --------------------- |
| Center along the horizontal flow | `margin-inline: auto` |
| Add space above a section        | `margin-block-start`  |
| Add space below a heading        | `margin-block-end`    |
| Add vertical interior spacing    | `padding-block`       |
| Separate Flexbox children        | `gap`                 |
| Add a line beneath a section     | `border-block-end`    |

This goes beyond saying that logical properties are “more modern.”

I wanted to demonstrate to myself that I could understand why each declaration matches a particular job.

It also documents a meaningful difference between the two projects: the first taught me individual CSS techniques, while the menu project required me to establish conventions for managing a much larger stylesheet.

### Improving Color Contrast

The original color palette included harvest gold, soft sage, forest green, and warm paper. Although the colors worked together visually, some combinations did not provide enough contrast when used for text. In particular, the original harvest gold and soft sage produced contrast ratios of approximately `3:1` against the warm-paper background.

I used the browser’s developer tools to inspect these combinations and test the suggested accessible alternatives. Instead of replacing the palette, I created darker versions of the existing colors:

```css
--deep-forest: #18392b;
--dark-sage: #495247;
--soft-sage: #71806f;
--dark-harvest-gold: #6d481a;
--harvest-gold: #b7792b;
--warm-paper: #f5efe3;
```

The darker variants could be used where stronger text contrast was required, while the original colors remained available for borders, accents, and other decorative elements.

I then assigned the colors according to their purpose through semantic custom properties:

```css
--color-page: var(--warm-paper);
--color-text: var(--dark-ink);
--color-muted: var(--dark-sage);
--color-heading: var(--deep-forest);
--color-link: var(--dark-harvest-gold);
--color-rule: var(--light-rule);
--color-accent: var(--harvest-gold);
```

This separated the raw palette from how each color is used. For example, `--color-link` describes a role, while `--dark-harvest-gold` describes the actual color assigned to that role.

The solution allowed me to preserve the intended earthy color palette without using lower-contrast colors for important text. I learned that a color can still remain part of a design even when it is not suitable for every purpose. Lighter or lower-contrast colors can be reserved for decoration, while darker variants support readable text and interactive elements.

The main lessons were:

- Similar-looking colors can perform very differently against the same background.
- A color appropriate for a border or accent may not be appropriate for body text.
- Color contrast should be tested in context rather than judged only by appearance.
- Accessible adjustments do not necessarily require abandoning the original palette.
- Primitive and semantic tokens make it easier to change a color without rewriting component rules.

That last point is especially important: I didn’t just find darker hex values. I built a system that separates **what a color is** from **what job it performs**.

---

## What I Learned

This project gave me practical experience with building a responsive layout from a mobile-first starting point, even though I did not initially realize that was the approach I had taken.

I developed a clearer understanding of Flexbox wrapping, `flex-basis`, calculated widths, media queries, logical properties, and the difference between primitive and semantic design tokens. I also learned that typography, realistic content, and spacing need to be considered while constructing the layout rather than added only after the structure is complete.

The project contained substantially more CSS than my first independent challenge. As a result, keeping declarations, selectors, tokens, and component sections organized became part of solving the design itself. The largest lesson was that maintainable CSS depends not only on whether a rule works, but also on whether its purpose and relationship to the rest of the stylesheet remain understandable.

---

## Contributing

Constructive feedback, accessibility suggestions, and improvements are welcome. This project is part of my learning process, so contributions that explain the reasoning behind a proposed change are especially helpful.

Before opening an issue or submitting a pull request, please read the [contribution guidelines](CONTRIBUTING.md).

---

## License

This project is available under the [MIT License](LICENSE).
