StripeI18n
==========

[![Build
Status](https://secure.travis-ci.org/ekosz/stripe-i18n.png)](http://travis-ci.org/ekosz/stripe-i18n)

The gem adds a collection of translated error strings for 'All Stripe errors'

**Supported Locales:**

1. en (English - US)
1. es (Spanish)
1. de (German)
1. fr (French)
1. it (Italian)
1. nl (Dutch) -- TODO
1. pt-BR (Portuguese - Brazil) -- TODO
1. ru (Russian) -- TODO
1. nb (Norwegian) -- TODO
1. ja (Japanese) -- TODO
1. zh-HK (Chinese - Hong Kong) -- TODO

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'stripe-i18n'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install stripe-i18n

## Usage
add this method in your helper.rb

def flash_stripe_errors(stripe_error)
    body = stripe_error.json_body
    err  = body[:error] if body
    code = err[:code] if err
    if stripe_error.is_a?(Stripe::APIConnectionError)
      msg = I18n.t("stripe.errors.connection_error")
    else
      if err[:decline_code]
        msg = I18n.t("stripe.errors.#{code}") + " " + I18n.t("stripe.errors.reason") + ": " +            I18n.t("stripe.errors.decline_reasons.#{err[:decline_code]}")
      else
        msg = I18n.t("stripe.errors.#{code}")
      end
    end
    flash[:alert] = msg
 end
  
  
Use the code on the error object to get the correct
translation key.

```ruby
def charge_token(token, amount)
  Stripe::Charge.create(
    amount: amount,
    currency: 'usd',
    card: token,
  )

  { success: true, msg: I18n.translate('charge.success') }
rescue Stripe::CardError, Stripe::RateLimitError, Stripe::APIConnectionError, Stripe::InvalidRequestError, Stripe::AuthenticationError, Stripe::StripeError => e
    flash_stripe_errors(e)
end
```

## Contributing

1. Fork it ( https://github.com/ekosz/stripe-i18n/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
