---
layout: post
author: yangtze736
title: 状态迁移表
category: 工作
tags: Linux
keywords:
description:
---


``` java

public enum OrderStatus {
    CREATED, // 订单创建
    UNPAID, // 订单待支付
    PAID, // 订单已支付
    TO_BE_SHIPPED, // 订单待发货
    SHIPPED, // 订单已发货
    CONFIRMED, // 订单确认
    COMPLETED, // 交易完成
    CANCELED // 订单已取消
}

public enum OrderEvent {
    PAY, // 支付
    SHIP, // 发货
    RECEIVE, // 确认收货
    CANCEL // 取消订单
}


public class OrderStateMachine {
    private static final Map<OrderStatus, Map<OrderEvent, OrderStatus>> stateMachine = new HashMap<>();
    static {
        // 定义状态转移表
        Map<OrderEvent, OrderStatus> createdTransitions = new EnumMap<>(OrderEvent.class);
        createdTransitions.put(OrderEvent.PAY, OrderStatus.UNPAID);
        createdTransitions.put(OrderEvent.CANCEL, OrderStatus.CANCELED);
        stateMachine.put(OrderStatus.CREATED, createdTransitions);

        Map<OrderEvent, OrderStatus> unpaidTransitions = new EnumMap<>(OrderEvent.class);
        unpaidTransitions.put(OrderEvent.PAY, OrderStatus.PAID);
        unpaidTransitions.put(OrderEvent.CANCEL, OrderStatus.CANCELED);
        stateMachine.put(OrderStatus.UNPAID, unpaidTransitions);

        Map<OrderEvent, OrderStatus> paidTransitions = new EnumMap<>(OrderEvent.class);
        paidTransitions.put(OrderEvent.SHIP, OrderStatus.TO_BE_SHIPPED);
        paidTransitions.put(OrderEvent.CANCEL, OrderStatus.CANCELED);
        stateMachine.put(OrderStatus.PAID, paidTransitions);

        Map<OrderEvent, OrderStatus> toBeShippedTransitions = new EnumMap<>(OrderEvent.class);
        toBeShippedTransitions.put(OrderEvent.RECEIVE, OrderStatus.CONFIRMED);
        toBeShippedTransitions.put(OrderEvent.CANCEL, OrderStatus.CANCELED);
        stateMachine.put(OrderStatus.TO_BE_SHIPPED, toBeShippedTransitions);

        Map<OrderEvent, OrderStatus> shippedTransitions = new EnumMap<>(OrderEvent.class);
        shippedTransitions.put(OrderEvent.RECEIVE, OrderStatus.CONFIRMED);
        shippedTransitions.put(OrderEvent.CANCEL, OrderStatus.CANCELED);
        stateMachine.put(OrderStatus.SHIPPED, shippedTransitions);

        Map<OrderEvent, OrderStatus> confirmedTransitions = new EnumMap<>(OrderEvent.class);
        confirmedTransitions.put(OrderEvent.CANCEL, OrderStatus.COMPLETED);
        stateMachine.put(OrderStatus.CONFIRMED, confirmedTransitions);
    }

    public static OrderStatus nextState(OrderStatus currentStatus, OrderEvent event) {
        Map<OrderEvent, OrderStatus> transitions = stateMachine.get(currentStatus);
        if (transitions == null) {
            throw new IllegalStateException("Invalid order status: " + currentStatus);
        }
        OrderStatus nextStatus = transitions.get(event);
        if (nextStatus == null) {
            throw new IllegalStateException("Invalid event " + event + " for order status " + currentStatus);
        }
        return nextStatus;
    }
}
```

假设我们有一个订单 order，它的状态为 ORDER_CREATED，接下来我们需要将它流转到 ORDER_PAID 状态，那么可以按照以下流程：

判断订单当前状态是否为 ORDER_CREATED
如果是，则将订单状态设置为 ORDER_TO_BE_PAID，并且更新订单的 update_time，同时添加一条状态变更记录到订单状态表中，记录的事件为 PAY_ORDER，表示用户支付订单
进入支付流程，等待用户支付
用户支付成功后，将订单状态设置为 ORDER_PAID，并且更新订单的 update_time，同时添加一条状态变更记录到订单状态表中，记录的事件为 ORDER_PAID，表示订单已支付

``` java
if (order.getOrderStatus() == OrderStatus.ORDER_CREATED) {
    order.setOrderStatus(OrderStatus.ORDER_TO_BE_PAID);
    order.setUpdateTime(new Date());
    orderStatusService.createOrderStatus(order.getOrderId(), OrderStatusEvent.PAY_ORDER, OrderStatus.ORDER_TO_BE_PAID);
    
    // 进入支付流程等待用户支付
    // ...
    
    order.setOrderStatus(OrderStatus.ORDER_PAID);
    order.setUpdateTime(new Date());
    orderStatusService.createOrderStatus(order.getOrderId(), OrderStatusEvent.ORDER_PAID, OrderStatus.ORDER_PAID);
}

```


[原文](https://www.cnblogs.com/huaizuo/p/17239107.html)
