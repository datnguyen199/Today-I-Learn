``` ruby
upcoming_subscription = Stripe::Subscription.create(
  customer: customer.id,
  items: [
    {
      plan: 'plan_id'  # Replace with the actual plan ID
    }
  ],
  billing_cycle_anchor: Time.now.to_i + 3600  # Set the billing cycle to start in 1 hour
)

if upcoming_subscription.status == 'not_started'
  # Upcoming subscription created successfully
else
  # Failed to create upcoming subscription
end
```
