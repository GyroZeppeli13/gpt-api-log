### 代码评审

#### 1. 文件重命名
- **IGptAccountService.xml -> user_account_mapper.xml**
  - **评审意见**：重命名文件应确保所有引用该文件的地方都进行了相应的更新，以避免编译错误或运行时错误。建议检查项目中所有引用该XML文件的代码，确保一致性。

#### 2. IOrderRepository.java
- **新增方法**：`void publishGoods(String orderId);`
  - **评审意见**：新增方法的命名应遵循现有代码的命名规范。建议在接口文档中添加该方法的具体描述和使用场景，以便其他开发者理解。

#### 3. AbstractOrderService.java
- **包名变更**：从`cn.happy.domain.order.service`变为`cn.happy.domain.order.service.order`
  - **评审意见**：包名变更应确保所有引用该类的代码都进行了相应的更新。建议检查项目中所有引用该类的代码，确保一致性。
  - **新增导入**：`import cn.happy.domain.order.service.IOrderService;`
  - **评审意见**：新增导入应确保其必要性，避免不必要的依赖。

#### 4. OrderService.java
- **包名变更**：从`cn.happy.domain.order.service`变为`cn.happy.domain.order.service.order`
  - **评审意见**：同上，确保所有引用该类的代码都进行了相应的更新。
  - **代码注释**：`// TODO 数据库有幂等拦截，如果有重复的订单ID会报错主键冲突。如果是公司里一般会有专门的雪花算法UUID服务`
  - **评审意见**：TODO注释应尽快处理，避免遗留问题。建议引入雪花算法生成唯一订单ID，确保系统的健壮性。

#### 5. OrderRepository.java
- **新增导入**：`import cn.happy.domain.order.event.SendRebateMessageEvent;`
  - **评审意见**：确保新增导入的必要性。
  - **事务注解变更**：`@Transactional`的`timeout`从350改为1500
  - **评审意见**：调整事务超时时间应基于实际业务需求和测试结果，确保不会因超时导致事务失败。
  - **新增返利积分逻辑**：
  ```java
  // 通过 MQ 请求大营销服务返利积分；积分与支付金额相等
  SendRebateMessageEvent.RebateMessage rebateMessage = SendRebateMessageEvent.RebateMessage.builder()
          .userId(openAIOrderPO.getOpenid())
          .rebateDesc("gpt服务支付返利积分")
          .rebateType("integral")
          .rebateConfig(openAIOrderPO.getTotalAmount().toPlainString())
          .bizId(openAIOrderPO.getOrderId())
          .build();

  eventPublisher.publish(sendRebateMessageEvent.topic(), sendRebateMessageEvent.buildEventMessage(rebateMessage));
  ```
  - **评审意见**：新增逻辑应确保与现有系统集成无误，建议添加单元测试和集成测试，验证返利积分功能的正确性。

#### 6. SaleController.java
- **删除导入**：`import javax.servlet.http.HttpServletResponse;`、`import java.io.IOException;`、`import java.math.RoundingMode;`
  - **评审意见**：删除未使用的导入是好的实践，但应确保确实未使用这些类。
  - **删除代码**：`// TODO 发货完成后通过 MQ 请求大营销服务返利积分；积分暂定与支付金额相等`
  - **评审意见**：TODO注释已实现，应确保功能完整且测试通过。

#### 7. OrderPaySuccessListener.java
- **新增日志**：`log.info("支付完成，发货并记录，成功。订单：{}", orderId);`
  - **评审意见**：增加日志有助于调试和监控，建议保持日志的一致性和详细性。

### 总结
- **文件和包名变更**：确保所有引用都更新一致。
- **新增逻辑**：确保功能完整且经过充分测试。
- **事务超时调整**：基于实际需求调整，确保系统稳定性。
- **TODO注释**：尽快处理，避免遗留问题。

整体来看，代码变更较为合理，但需注意细节处理和测试验证，确保系统稳定性和功能的正确性。