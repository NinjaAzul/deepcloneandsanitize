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
