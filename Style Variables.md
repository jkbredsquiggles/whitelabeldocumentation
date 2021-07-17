# Global Style Variables (any of these can be overridden per-room):

```
{
  "active-route-link-colour": "red",
  "primary-colour": "#e8bc43",
  "primary-gradient-colour": "#edc34e",
  "primary-text-colour": "black",
  "secondary-colour": "#d56ef5",
  "secondary-gradient-colour": "#e07aff",
  "secondary-text-colour": "black",
  "hover-brightness": "108%"
}
```

- active-route-link-colour: Color of the selected item in the nav
- primary-colour: used for
  - lobby bg color
  - breakout hall item color
  - table hall item color
  - video library item color
  - vendor item color
- secondary-colour: used for
  - breakout hall 'JOIN' button color
  - lobby mobile button color



# TableHall Style Variables:

```
{
  "table-max-width": "200px",
  "num-columns": 3,
  "column-spacing": "20px",
  "row-spacing": "20px",
  "item-width": "180px",
  "item-height": "180px",
  "translate-x": "0px",
  "translate-y": "0px"
}
```

- item-width/height: For images or items in tables, height and width. Width is clipped to table-max-width
- column/row spacing: spacing between tables, in px
- translate-x/y: moves the entire page's grid of tables by px. Careful, each browser can have different sizing/zoom

# Per item style variables

{
  "active-route-link-colour": "red",
  "primary-colour": "#e8bc43",
  "primary-gradient-colour": "#edc34e",
  "primary-text-colour": "black",
  "secondary-colour": "#d56ef5",
  "secondary-gradient-colour": "#e07aff",
  "secondary-text-colour": "black",
}

As defined above, defined at global level, but can be overridden at table and item level
