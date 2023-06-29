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

``` ruby
subscription_schedule = Stripe::SubscriptionSchedule.create(
  customer: 'customer_id',  # Replace with the actual customer ID
  start_date: Time.now.to_i + 3600,  # Set the start date to be 1 hour from now
  end_behavior: 'release',
  phases: [
    {
      items: [
        {
          id: 'subscription_item_id',  # Replace with the actual subscription item ID
          plan: 'new_plan_id',  # Replace with the ID of the new plan
        }
      ],
      iterations: 1,
    },
  ],
)

if subscription_schedule.status == 'active'
  # Subscription schedule created successfully
else
  # Failed to create subscription schedule
end
```
- stripe listen --forward-to localhost:3000/api/v1/stripe-webhooks/webhook --events checkout.session.completed,customer.subscription.deleted,customer.subscription.created --api-key <SECRET_KEY>
- to use webhook by cli: change ENV[STRIPE_WEBHOOK_SECRET] in .env file to webhook signing secret generate by cli
