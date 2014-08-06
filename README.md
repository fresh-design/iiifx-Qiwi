Qiwi
=======

Модуль оплаты Qiwi

##### Подключение через Comoposer:

    "require": {
        ...
        "fresh-design/iiifx-Qiwi": "dev-master"
    },
    "repositories": [
        {
            "type": "git",
            "url": "https://github.com/fresh-design/iiifx-Qiwi"
        }
    ]

### Использование

###### Выставить счет клиенту и отправить на оплату:

```php
    $qiwiPaymentRequest = new \iiifx\Component\Payment\Qiwi\PaymentRequest( $shopId, $apiId, $apiPassword );
    # Задаем данные платежа
    $qiwiPaymentRequest
        ->setClientPhone( $clientPhone ) # Телефон клиента: +380990009900
        ->setPaymentAmount( $orderAmount ) # Сумма оплаты: 111.00
        ->setCurrencyCode( $currencyCode ) # Валюта, трехсимвольный код ISO4217: RUB
        ->setPaymentComment( "Оплата заказа #{$orderID}" ) # Комментарий к оплате
        ->setLifetimeSeconds( \iiifx\Component\Payment\Qiwi\PaymentRequest::LifetimeSeconds_OneDay ) # Время жизни счета, в секундах
        ->setPaymentType( \iiifx\Component\Payment\Qiwi\PaymentRequest::PaymentType_Any ) # Тип оплаты
        ->setShopName( 'WePlay' );
    if ( !$qiwiPaymentRequest->validateData() ) {
        # Ошибка данных оплаты
    } else {
        # Делаем запрос, получаем ответ
        $qiwiResponse = $qiwiPaymentRequest->doRequest();
        # Проверяем результат
        if ( $qiwiResponse->isSuccess() ) {
            # $transactionId = $qiwiPaymentRequest->getTransactionId();
            # Ссылка на редирект пользователя, для оплаты
            echo $qiwiResponse->getClientRedireckLink();
        } else {
            # Код ошибки
            echo $qiwiResponse->getErrorCode();
        }
    }
```

###### Проверить статус оплаты

```php
    $qiwiCheckRequest = new \iiifx\Component\Payment\Qiwi\CheckRequest( $shopId, $apiId, $apiPassword );
    # Устанавливаем номер транзакции
    $qiwiCheckRequest->setTransactionId( $transactionId ); # ID транзакции, который был сохранен в момент создания счета
    # Делаем запрос, получаем ответ
    $qiwiResponse = $qiwiCheckRequest->doRequest();
    # Проверяем результат
    if ( $qiwiResponse->isSuccess() ) {
        if ( $qiwiResponse->isPaidStatus() ) {
            # Заказ успешно оплачен
        } elseif ( $qiwiResponse->isWaitingStatus() ) {
            # Оплата заказа ожидается
        } else {
            # Оплата была отменена или просрочена
        }
    } else {
        # Не удалось проверить статус оплаты
        echo $qiwiResponse->getErrorCode();
    }
```