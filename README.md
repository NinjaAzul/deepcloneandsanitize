# deepcloneandsanitize

```ts

type GenericPlainObject = Record<string, unknown> | Array<Record<string, unknown>> | any;

export function deepClone<T extends GenericPlainObject>(value: T): T {
  if (typeof value !== 'object' || value === null) return value;
  if (Array.isArray(value)) return value.map(deepClone) as T;
  const clonedObj = {} as T;
  Object.keys(value).forEach((key) => (clonedObj[key] = deepClone(value[key])));
  return clonedObj;
}

export function sanitizeObjects<T extends GenericPlainObject>(value: T): T {
  if (value === null || typeof value !== 'object') return value;
  if (Array.isArray(value)) return value.map(sanitizeObjects) as T;
  const sanitizedObj = {} as T;
  for (const [key, val] of Object.entries(value)) {
    if (key === 'password') continue;
    if (typeof val === 'object') sanitizedObj[key] = sanitizeObjects(val);
    else sanitizedObj[key] = val;
  }
  return sanitizedObj;
}
```

```ts

export function convertKeysToCamelCase(obj: Record<string, unknown>): Record<string, unknown> {
  const result: Record<string, unknown> = {};
  for (const [key, value] of Object.entries(obj)) {
    const camelCaseKey = key
      .replace(/(?:^\w|[A-Z]|\b\w)/g, (letter, index) =>
        index === 0 ? letter.toLowerCase() : letter.toUpperCase()
      )
      .replace(/\s+/g, '');
    if (typeof value === 'object' && value !== null && !Array.isArray(value)) {
      result[camelCaseKey] = convertKeysToCamelCase(value as Record<string, unknown>);
    } else if (Array.isArray(value)) {
      result[camelCaseKey] = value.map((item) =>
        typeof item === 'object' && item !== null
          ? convertKeysToCamelCase(item as Record<string, unknown>)
          : item
      );
    } else {
      result[camelCaseKey] = value;
    }
  }
  return result;
}

```
