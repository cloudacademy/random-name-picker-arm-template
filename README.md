# random-name-picker-arm-template

This template randomly pairs adjectives and verbs from arrays to create random names such as:

- cyan-dragon
- gold-hamster
- and more (400 combinations by default)

It is performed completely using native ARM template functions. and takes around 30 seconds to deploy.

## Usage

Include the following linked template resource in your template:

```json
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2022-09-01",
      "name": "randomNameTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://raw.githubusercontent.com/cloudacademy/random-name-picker-arm-template/main/azure-deploy.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
```

Reference the template's `randomName` output to get the random name in your template:

```json
reference('randomNameTemplate').outputs.randomName.value
```

For example in your template outputs:

```json
"outputs": {
    "randomName": {
        "type": "string",
        "value": "[reference('randomNameTemplate').outputs.randomName.value]"
    }
}
```

## Algorithm

The first integer characters (from left to right) in two GUIDs are used for random numbers (1 is appended in case no integers are in the GUIDs). Each digit is concatenated (with the leading digit taken modulo 2) to form an index from 00-19. This operation is reversed (the second digit is used modulo 2 followed by the first digit) to form a second index. Each index is used to index the adjective and noun arrays to create the random name. The [copy element](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/copy-variables) in the `variables` is used to transform the GUID strings into arrays of characters that can be intersected with digits to randomly select a number between 0 and 9.
