# Model

# Enumeration

An enum (short for enumeration) is a data type that consists of a set of named constant values. It allows you to define a variable that can only hold one value from a predefined list of options, making code more readable and less prone to errors. 

## Label

```ts
export enum LabelSize {
  Size4x3 = '4x3',
  Size4x4 = '4x4',
  Size4x6 = '4x6',
  Size6x4 = '6x4',
  SizeA4 = 'A4',
  Size80mm = '80mm',
}
```

```ts
export enum LabelOrientation {
  Portrait = 'Portrait',
  Landscape = 'Landscape',
}
```

## Order shipment

```ts
export enum OrderShipmentChannel {
    V1 = 'V1',
    V2 = 'V2',
    V3 = 'V3',
    UPLOAD_SHEET = 'UPLOAD_SHEET',
    API = 'API',
}
```

```ts
export enum OrderShipmentStatus {
  Prepared = 'Prepared',
  WaitForDropOff = 'WaitForDropOff',
  InTransit = 'In-Transit',
  Cancel = 'Cancel',
  Complete = 'Complete',
  Reject = 'Reject',
  Claim = 'Claim',
}
```

```ts
export enum FlashArticleCategory {
  File = 0,
  DryFood = 1,
  Commodity = 2,
  DigitalProduct = 3,
  Clothes = 4,
  Books = 5,
  AutoParts = 6,
  ShoesAndBags = 7,
  SportsEquipment = 8,
  Cosmetics = 9,
  Household = 10,
  Fruit = 11, // When the item type is fruit, the product type will be fruit by default.
  Others = 99,
}
```

```ts
export enum PlanDetailRegion {
  BKK = 'BKK',
  UPC = 'UPC',
}
```

```ts
export enum PlanDetailType {
  Weight = 'weight',
  Dimension = 'dimension',
  Cubic = 'cubic',
}
```

# Class

# Interface

```ts
export interface FileHttp {
    file_name: string;
    content: string;
    content_type: string;
    content_length: number;
}
```

```ts
export interface HttpBaseResponse<T> {
  code: number;
  isSuccess: boolean;
  message: string;
  data: T;
}
```