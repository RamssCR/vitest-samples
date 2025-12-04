# Ejemplos básicos de Vitest

1. Estructura básica de un test
```JS
import { describe, expect, test } from 'vitest'

/**
 * Suma dos parámetros
 * @param {number} a - valor 1.
 * @param {number} b - valor 2.
 * @returns {number} La suma de ambos valores
 */
const add = (a, b) => a + b

describe('Suite de sumas', () => {
  test('suma dos numeros correctamente', () => {
    const addition = add(3, 6)
    expect(addition).toEqual(9) // o también .toBe(9)
  })
})
```

2. Capturar valores específicos
```JS
import { expect, it } from 'vitest'

it('valida que un string contenga un valor especifico', () => {
  const value = "Mi constante con varias palabras"
  expect(value).toContain('constante')
  expect(value).not.toContain('ñ')
})
```

3. Valida promesas
```JS
import { describe, expect, it } from 'vitest'

describe('Promesas', () => {
  it('resuelve la promesa', () => {
    const action = new Promise((resolve) => resolve('promesa resuelta'))
    expect(action).resolves.toBe('promesa resuelta')
  })
})
```
