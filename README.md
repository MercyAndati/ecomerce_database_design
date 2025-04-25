# ecomerce_database_design
link to ERD diagram : https://lucid.app/lucidchart/62bc0058-a190-4d19-b61a-a8f707d58335/edit?viewport_loc=-800%2C-957%2C2628%2C1089%2C0_0&invitationId=inv_0f4e4831-a0f9-4576-82a4-7486eccded36

**Database Tables & Relationships**
**1. Brand (brand)**
Stores brand information.

PK: brand_id

*Fields:*

brand_name (VARCHAR)

brand_description (TEXT)

created_at, updated_at (TIMESTAMP)

*Relationships:*

1 → Many with product (A brand can have multiple products).

**2. Product Category (product_category)**
Classifies products into hierarchical categories (e.g., Clothing → Men → T-Shirts).

PK: category_id

*Fields:*

category_name (VARCHAR)

parent_category_id (INT, self-referential FK)

created_at, updated_at (TIMESTAMP)

*Relationships:*

1 → Many with product (A category can contain multiple products).

Self-referential (Supports nested categories).

**3. Product (product)**
Stores core product details.

PK: product_id

*Fields:*

product_name (VARCHAR)

product_description (TEXT)

brand_id (FK → brand)

category_id (FK → product_category)

base_price (DECIMAL)

created_at, updated_at (TIMESTAMP)

*Relationships:*

Many → 1 with brand (Each product belongs to a brand).

Many → 1 with product_category (Each product belongs to a category).

1 → Many with product_image, product_variation, product_attribute.

**4. Product Image (product_image)**
Stores image URLs for products.

PK: image_id

*Fields:*

product_id (FK → product)

image_url (VARCHAR)

is_primary (BOOLEAN)

created_at (TIMESTAMP)

*Relationships:*

Many → 1 with product (Multiple images per product).

**5. Size Category (size_category)**
Groups sizes (e.g., Clothing, Shoes).

PK: size_category_id

*Fields:*

category_name (VARCHAR)

description (TEXT)

created_at (TIMESTAMP)

*Relationships:*

1 → Many with size_option (A category contains multiple sizes).

**6. Size Option (size_option)**
Defines specific sizes (e.g., S, M, L, 42).

PK: size_id

*Fields:*

size_category_id (FK → size_category)

size_value (VARCHAR)

size_order (INT, for sorting)

created_at (TIMESTAMP)

*Relationships:*

Many → 1 with size_category (Each size belongs to a category).

1 → Many with product_variation (A size can be used in multiple variations).

**7. Color (color)**
Stores available color options.

PK: color_id

*Fields:*

color_name (VARCHAR)

hex_code (VARCHAR, for UI display)

created_at (TIMESTAMP)

*Relationships:*

1 → Many with product_variation (A color can be used in multiple variations).

**8. Product Variation (product_variation)**
Defines product variants (combinations of size, color, etc.).

PK: variation_id

*Fields:*

product_id (FK → product)

size_id (FK → size_option, nullable)

color_id (FK → color, nullable)

sku (VARCHAR, unique identifier)

created_at (TIMESTAMP)

*Relationships:*

Many → 1 with product (A product can have multiple variations).

1 → 1 with product_item (Each variation corresponds to a purchasable item).

**9. Product Item (product_item)**
Tracks inventory for each variation.

PK: item_id

*Fields:*

variation_id (FK → product_variation)

quantity_in_stock (INT)

price_adjustment (DECIMAL, for variant-specific pricing)

created_at, updated_at (TIMESTAMP)

*Relationships:*

1 → 1 with product_variation (Each item is a specific variant).

**10. Attribute Type (attribute_type)**
Defines data types for attributes (e.g., text, number, boolean).

PK: attribute_type_id

*Fields:*

type_name (VARCHAR)

data_type (ENUM: text, number, boolean, date)

created_at (TIMESTAMP)

*Relationships:*

1 → Many with product_attribute (An attribute type can be used in multiple attributes).

**11. Attribute Category (attribute_category)**
Groups attributes (e.g., "Physical", "Technical").

PK: attribute_category_id

*Fields:*

category_name (VARCHAR)

description (TEXT)

created_at (TIMESTAMP)

*Relationships:*

1 → Many with product_attribute (A category can contain multiple attributes).

**12. Product Attribute (product_attribute)**
Stores custom product details (e.g., material, weight).

PK: attribute_id

*Fields:*

product_id (FK → product)

attribute_category_id (FK → attribute_category)

attribute_type_id (FK → attribute_type)

attribute_name (VARCHAR)

attribute_value (TEXT)

created_at, updated_at (TIMESTAMP)

*Relationships:*

Many → 1 with product (A product can have multiple attributes).

Many → 1 with attribute_category and attribute_type.

**🔑 Key Constraints**
ON DELETE CASCADE (Child records are deleted if parent is deleted):

product_image, product_variation, product_attribute (when product is deleted).

ON DELETE RESTRICT (Prevents deletion if child records exist):

product_category (if products are assigned).

ON DELETE SET NULL (Foreign key set to NULL if parent is deleted):

size_id, color_id in product_variation.

**📊 Summary of Relationships**
*Parent Table	Child Table	Relationship*
brand	product	1 → Many
product_category	product	1 → Many
product	product_image	1 → Many
product	product_variation	1 → Many
product_variation	product_item	1 → 1
size_category	size_option	1 → Many
size_option	product_variation	1 → Many
color	product_variation	1 → Many
product	product_attribute	1 → Many
attribute_category	product_attribute	1 → Many
attribute_type	product_attribute	1 → Many
