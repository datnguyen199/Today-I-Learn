``` ruby
module PricingEngine
  class PricingRepositoryInterface
    def variants_by_ids(ids)
      raise NotImplemented
    end

    def variants_by_titles(titles)
      raise NotImplemented
    end
  end

  module Schema
    class Variant
      attr_accessor :title
      attr_accessor :price

      def initialize(title:, price:)
        @title = title
        @price = price
      end
    end
  end
end
```

``` ruby
# Consumer 1 Logic

require 'pricing_engine'

module ShopifyCore
  class PricingRepository < PricingEngine::PricingRepositoryInterface
    def variants_by_ids(ids)
      variants = ProductVariant.where(id: ids).select(:title, :price).limit(50)
      variants.map { |variant| PricingEngine::Schema::Variant.new(title: variant.title, price: variant.price) }
    end

    def variants_by_titles(titles)
      variants = ProductVariant.where(title: titles).select(:title, :price).limit(50)
      variants.map { |variant| PricingEngine::Schema::Variant.new(title: variant.title, price: variant.price) }
    end
  end
end
```

``` ruby
# Consumer 2 Logic

require 'pricing_engine'

module StorefrontRenderer
  class PricingRepository < PricingEngine::PricingRepositoryInterface
    def variants_by_ids(ids)
      variants = DataStoreConnection.execute("SELECT title, price FROM variants WHERE id in (?)", ids).limit(50)
      variants.map { |variant| PricingEngine::Schema::Variant.new(title: variant.title, price: variant.price) }
    end

    def variants_by_titles(titles)
      variants = DataStoreConnection.execute("SELECT title, price FROM variants WHERE name in (?)", titles).limit(50)
      variants.map { |variant| PricingEngine::Schema::Variant.new(title: variant.title, price: variant.price) }
    end
  end
end
```

``` ruby
module PricingEngine
  class Engine
    # @param repository [PricingEngine::PricingRepositoryInterface] environment specific functions to
    # access db and cache data.
    #
    # @return [void]
    def initialize(repository)
      @repository = repository
    end

    #...
    # Calculates the price for variants given the buyer context.
    #
    # @param context [Schema::BuyerContext] the buyer context.
    #
    # @return [Array<PricingEngine::Schema::Price>] An array of prices for the context passed.
    def calculate_prices_for_variants(context)
      variant_ids = get_variant_ids_from_context(context)
      variants = @repository.variants_by_ids(variant_ids)
      process(variants)
    end
  end
end
```
