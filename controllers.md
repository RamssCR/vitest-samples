# Controllers
Ejemplos de pruebas de un controller

```JS
// controllers/tenant.js
import { getTenants as getTenantService } from '#services/tenant'
/** @import { NextFunction, Request, Response } from 'express' */

/**
 * Obtiene y devuelve todos los tenants.
 * @param {Request} req - Solicitud autenticada.
 * @param {Response} res - Respuesta HTTP.
 * @param {NextFunction} next - Función para pasar al siguiente middleware.
 * @returns {Promise<void>} - Una promesa que resuelve cuando la respuesta ha sido enviada.
 * @example
 * router.get('/tenants', getTenants)
 */
export const getTenants = async (req, res, next) => {
  const { page, limit, offset } = req.query

  try {
    const tenants = await getTenantsService({ page, limit, offset })
    res.json({
      status: 'success',
      message: 'Tenants obtenidos exitósamente',
      data: tenants,
    })
  } catch (error) {
    next(error)
  }
}
```
```JS
// controllers/tenant.spec.js
import { createRequest, createResponse } from 'node-mocks-http'
import { describe, expect, test, vi } from 'vitest'
import { getTenants } from '#controllers/tenant'
import { getTenants as getTenantsService } from '#services/tenant'

vi.mock('#services/tenant')

describe('Suite de pruebas para controllers', () => {
  test('obtiene los tenants correctamente', async () => {
    const req = createRequest({
      method: 'GET',
      url: '/tenants',
      query: { page: 1, limit: 10 },
    })
    const res = createResponse()
    const next = vi.fn()

    const mockTenants = { items: [], page: 1, limit: 10, total: 0, pages: 0 }
    getTenantsService.mockResolvedValue(mockTenants)

    await getTenants(req, res, next)
    expect(getTenantsService).toHaveBeenCalledWith({ page: 1, limit: 10 })
  })

  test('lanza un error al intentar obtener todos los tenants', async () => {
    const req = createRequest({
      method: 'GET',
      url: '/tenants',
      query: { page: 1, limit: 10 },
    })
    const res = createResponse()
    const next = vi.fn()

    const errorMessage = 'Error al obtener tenants'
    getTenantsService.mockRejectedValue(new Error(errorMessage))

    await getTenants(req, res, next)

    expect(next).toHaveBeenCalledWith(expect.objectContaining({ message: errorMessage }))
  })
})
```
