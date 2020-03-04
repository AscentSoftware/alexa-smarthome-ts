# Alexa SmartHome Typescript

Typescript definitions for Alexa Smart Home.

## Why

Alexa SmartHome data model is quite complex. It is easy to forget mandatory properties or to return an unexpected state. Types can help us to save time. 

In particular,

* Invalid properties, states and payloads are detected at compile time.
* Editor autocompletion builds directives and responses without looking up the documentation.

Our goal is to build a "good enough" representation of the data model. Typescript is able to define not only the shape of an entity, but also
constraints between concepts (e.g. a capability can have a precise set of properties different from those of others).

Nevertheless, some features of the model might not be expressible in Typescript or require too hacky solutions. In those cases, we prefer the simplest approach even if it is not faithful to the reference model. Programming is always a compromise between expressivity/correctness and productivity.

## How it works

This package is based on [declaration merging and module augumentation](https://www.typescriptlang.org/docs/handbook/declaration-merging.html). In this way, it can be easily extended.

[Mapped types](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types) are used to enforce constraints between type definitions.

In particular:

* `Capabilities` is a table listing all available capabilities.
* `Properties` maps a capability to corresponding properties (used in discovery and state events),
* `Directives` maps a capability to corresponding directives.
* `Payloads` maps a directive to the corresponding input payload (if any).

When we add a new capability, we need to extend those mappings. 

First, add a new file with the capability's name in `capabilities` folder, e.g. `ColorTemperatureController` (see [here](https://developer.amazon.com/en-US/docs/alexa/device-apis/alexa-colortemperaturecontroller.html)).

Then, we encode the properties of `Alexa.ColorTemperatureController` capability interface in this way.

```typescript
// define `Alexa.ColorTemperatureController` capability
declare module '../base/Capabilities' {
  interface Capabilities {
    [COLOR_TEMPERATURE_CONTROLLER]: true;
  }
}

declare module '../base/Properties' {
  // `Alexa.ColorTemperatureController` has the following properties
  interface Properties {
    [COLOR_TEMPERATURE_CONTROLLER]: {
      colorTemperatureInKelvin: number;
    };
  }
}

declare module '../base/Directives' {
  // define the following directives for `Alexa.ColorTemperatureController` capability
  interface Directives {
    [COLOR_TEMPERATURE_CONTROLLER]: {
      [DECREASE_COLOR_TEMPERATURE_DIRECTIVE]: true;
      [INCREASE_COLOR_TEMPERATURE_DIRECTIVE]: true;
      [SET_COLOR_TEMPERATURE_DIRECTIVE]: true;
    };
  }
}

declare module '../base/Payloads' {
  // define payload shapes for directives
  interface Payloads {
    [DECREASE_COLOR_TEMPERATURE_DIRECTIVE]: {};
    [INCREASE_COLOR_TEMPERATURE_DIRECTIVE]: {};
    [SET_COLOR_TEMPERATURE_DIRECTIVE]: {
      colorTemperatureInKelvin: number;
    };
  }
}

```

It is convinient to define a type for each directives in `directives` folder. We add a file for each directive and use the type constructor `EndpointDirective` to build a type:

```typescript
type DecreaseColorTemperature =
  EndpointDirective<'Alexa.ColorTemperatureController', 'DecreaseColorTemperature'>
```

## Styling

* Follow [Typescript Deep Dive](https://basarat.gitbooks.io/typescript/).
* Add support for `editorconfig`, `tslint` and `prettier` to your editor.

## Contributing

The current implementation is still incomplete. We are looking for help to complete the library! PRs are welcome. ❤️

## License

MIT - Copyright (c) 2020 Ascent Software (www.ascent.software)

## References

* [Documentation](https://developer.amazon.com/docs/smarthome/smart-home-skill-api-message-reference.html)