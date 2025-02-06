根据提供的Git diff记录，以下是对代码变更的评审：

### coupon_mapper.xml
- **变更**：在`incrIssueNum`更新语句中添加了额外的条件`WHERE id = #{id} AND issue_num + #{add} <= total_num`。
- **评审**：这是一个重要的安全措施，可以防止优惠券的库存超发。它确保在增加发行数量之前，剩余数量不会小于0。这是一个好的实践，应该被保留。

### user_coupon_mapper.xml
- **变更**：移除了`queryCouponByUserCouponIds`和`countUserCoupon`的旧实现，并添加了新的实现。
- **评审**：移除旧的实现是合理的，如果旧实现有缺陷或者不再需要。新的实现应该经过测试以确保其正确性。
- **注意**：在`queryCouponByUserCouponIds`中，`userCouponIds`变量被重命名为`ids`，这是一个小的命名更改，通常不影响功能，但应该确保调用代码也相应更新。

### AbstractCouponService.java
- **变更**：`DefaultDiscountFactory`的导入路径从`rule.factory`更改为`service.rule.factory`。
- **评审**：这是一个命名空间更改，可能是因为重构或模块化。确保所有使用`DefaultDiscountFactory`的地方都已经更新。

### ILogicDiscount.java
- **变更**：文件名从`rule/ILogicDiscount.java`重命名为`service/rule/ILogicDiscount.java`。
- **评审**：这是一个命名空间更改，可能是为了更好地反映代码的结构或职责。确保所有引用`ILogicDiscount`的地方都已经更新。

### DefaultDiscountFactory.java, NoThresholdDiscount.java, PerPriceDiscount.java, PriceDiscount.java, RateDiscount.java
- **变更**：文件名从`rule/...`重命名为`service/rule/...`。
- **评审**：与上面相同，这是一个命名空间更改，可能是因为重构或模块化。确保所有引用这些类的代码都已经更新。

### CouponRepository.java
- **变更**：在`incrIssueNum`方法中添加了注释，说明使用乐观锁防止超发。
- **评审**：这是一个好的实践，注释清晰地说明了代码的目的和原因。

### IUserCouponDao.java
- **变更**：添加了`queryCouponByUserCouponIds`方法。
- **评审**：这是一个新的方法，应该经过测试以确保其正确性。

### 总结
- 所有更改都应经过充分的测试，以确保代码的稳定性和正确性。
- 命名空间更改应确保所有引用都得到更新，以避免潜在的错误。
- 添加的注释有助于其他开发者理解代码的目的和原因。