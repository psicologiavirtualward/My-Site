# WARD · landing + reservas

## Ejecutar
    npm install
    cp .env.example .env.local   # sin credenciales = MODO DEMO (solo desarrollo)
    npm run dev                  # http://localhost:3000

## Configurar
- Textos, servicios, horario, profesional, FAQ, políticas: `lib/config.ts` (los marcados EDITAR son provisionales).
- Precio: 30 USD por sesión (en `lib/config.ts`; se puede sobrescribir con `PRICE_PRIMERA|INDIVIDUAL|SEGUIMIENTO`).
- **PayPal**: crea una app en developer.paypal.com → `PAYPAL_CLIENT_ID/SECRET`, `PAYPAL_ENV=sandbox|live`.
  Webhook a `https://TU_DOMINIO/api/webhooks/paypal` con el evento `PAYMENT.CAPTURE.COMPLETED` → `PAYPAL_WEBHOOK_ID`.
- **Google Calendar**: cuenta de servicio, activa Calendar API, comparte tu calendario con su email (permiso de edición) → `GOOGLE_*`.
- Cambiar de pasarela: implementa `PaymentProvider` (`lib/payments/types.ts`) y cámbiala en `lib/payments/index.ts`.

## Seguridad / flujo
Servidor crea el pedido → PayPal → retorno `/reserva/retorno` captura y verifica con la API (estado, monto, moneda vs. config) → crea el evento. El webhook (firma verificada) hace lo mismo. El evento usa ID derivado del pedido: sin duplicados.
Pago verificado pero horario ocupado (carrera): se avisa y se registra para reembolso manual.
