# Esquema de Base de Datos para foodHub

## Tablas Principales

### **Users**
| Columna          | Tipo         | Restricciones               |
|------------------|--------------|-----------------------------|
| id               | SERIAL       | PRIMARY KEY                 |
| name             | VARCHAR(100) | NOT NULL                    |
| email            | VARCHAR(150) | UNIQUE, NOT NULL            |
| password_hash    | VARCHAR(255) | NOT NULL                    |
| phone            | VARCHAR(20)  |                             |
| address_id       | INT          | FOREIGN KEY (Addresses.id)  |
| role             | VARCHAR(20)  | DEFAULT 'customer'          |
| is_verified      | BOOLEAN      | DEFAULT FALSE               |
| created_at       | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP   |

### **Products**
| Columna            | Tipo         | Restricciones               |
|--------------------|--------------|-----------------------------|
| id                 | SERIAL       | PRIMARY KEY                 |
| name               | VARCHAR(150) | NOT NULL                    |
| description        | TEXT         |                             |
| price              | DECIMAL(10,2)| NOT NULL                    |
| preparation_time   | INT          |                             |
| is_available       | BOOLEAN      | DEFAULT TRUE                |
| is_featured        | BOOLEAN      | DEFAULT FALSE               |
| created_at         | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP   |
| updated_at         | TIMESTAMP    |                             |

### **Categories**
| Columna       | Tipo         | Restricciones               |
|--------------|--------------|-----------------------------|
| id           | SERIAL       | PRIMARY KEY                 |
| name         | VARCHAR(100) | NOT NULL                    |
| description  | TEXT         |                             |
| image_url    | VARCHAR(255) |                             |
| parent_id    | INT          | FOREIGN KEY (Categories.id) |
| is_featured  | BOOLEAN      | DEFAULT FALSE               |



### **Product_Category**
| Columna       | Tipo | Restricciones                     |
|--------------|------|-----------------------------------|
| product_id   | INT  | FOREIGN KEY (Products.id)        |
| category_id  | INT  | FOREIGN KEY (Categories.id)      |

### **Product_Images**
| Columna      | Tipo         | Restricciones               |
|-------------|--------------|-----------------------------|
| id          | SERIAL       | PRIMARY KEY                 |
| product_id  | INT          | FOREIGN KEY (Products.id)   |
| image_url   | VARCHAR(255) | NOT NULL                    |
| is_primary  | BOOLEAN      | DEFAULT FALSE               |


### **Orders**
| Columna            | Tipo         | Restricciones               |
|--------------------|--------------|-----------------------------|
| id                 | SERIAL       | PRIMARY KEY                 |
| user_id            | INT          | FOREIGN KEY (Users.id)      |
| status_id          | INT          | FOREIGN KEY (Order_Status.id)|
| total_amount       | DECIMAL(10,2)| NOT NULL                    |
| delivery_address   | TEXT         | NOT NULL                    |
| delivery_notes     | TEXT         |                             |
| delivery_time      | TIMESTAMP    |                             |
| delivery_method    | VARCHAR(20)  | CHECK (delivery_method IN ('delivery', 'pickup')) |
| created_at         | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP   |

### **Order_Details** 
| Columna          | Tipo         | Restricciones               |
|------------------|--------------|-----------------------------|
| id               | SERIAL       | PRIMARY KEY                 |
| order_id         | INT          | FOREIGN KEY (Orders.id)     |
| product_id       | INT          | FOREIGN KEY (Products.id)   |
| quantity         | INT          | NOT NULL                    |
| unit_price       | DECIMAL(10,2)| NOT NULL                    |
| subtotal         | DECIMAL(10,2)| NOT NULL                    |
| special_requests | TEXT         |                             |

### **Payments** 
| Columna          | Tipo         | Restricciones               |
|------------------|--------------|-----------------------------|
| id               | SERIAL       | PRIMARY KEY                 |
| order_id         | INT          | FOREIGN KEY (Orders.id)     |
| payment_method   | VARCHAR(50)  | NOT NULL                    |
| amount           | DECIMAL(10,2)| NOT NULL                    |
| transaction_id   | VARCHAR(100) |                             |
| status           | VARCHAR(20)  | NOT NULL                    |



### **Promotions** 
| Columna           | Tipo         | Restricciones               |
|-------------------|--------------|-----------------------------|
| id                | SERIAL       | PRIMARY KEY                 |
| name              | VARCHAR(100) | NOT NULL                    |
| discount_type     | VARCHAR(20)  | CHECK (discount_type IN ('percentage', 'fixed')) |
| discount_value    | DECIMAL(5,2) | NOT NULL                    |
| start_date        | DATE         | NOT NULL                    |
| end_date          | DATE         | NOT NULL                    |
| applicable_scope  | VARCHAR(20)  | CHECK (applicable_scope IN ('product', 'category', 'order')) |
| is_active         | BOOLEAN      | DEFAULT TRUE                |

### **Promotion_Targets** 
| Columna       | Tipo | Restricciones                     |
|--------------|------|-----------------------------------|
| promotion_id | INT  | FOREIGN KEY (Promotions.id)      |
| target_type  | VARCHAR(20) | CHECK (target_type IN ('product', 'category')) |
| target_id    | INT  |                                   |

### **Product_Options** 
| Columna          | Tipo         | Restricciones               |
|------------------|--------------|-----------------------------|
| id               | SERIAL       | PRIMARY KEY                 |
| product_id       | INT          | FOREIGN KEY (Products.id)   |
| option_name      | VARCHAR(50)  | NOT NULL                    |
| option_type      | VARCHAR(20)  | CHECK (option_type IN ('checkbox', 'select', 'radio')) |
| price_modifier   | DECIMAL(10,2)|                             |
| is_required      | BOOLEAN      | DEFAULT FALSE               |

### **Order_Options** 
| Columna          | Tipo | Restricciones                     |
|------------------|------|-----------------------------------|
| order_detail_id  | INT  | FOREIGN KEY (Order_Details.id)   |
| option_id        | INT  | FOREIGN KEY (Product_Options.id) |
| selected_value   | TEXT | NOT NULL                         |


### **Reviews** 
| Columna      | Tipo         | Restricciones               |
|-------------|--------------|-----------------------------|
| id          | SERIAL       | PRIMARY KEY                 |
| product_id  | INT          | FOREIGN KEY (Products.id)   |
| user_id     | INT          | FOREIGN KEY (Users.id)      |
| rating      | INT          | CHECK (rating BETWEEN 1 AND 5) |
| comment     | TEXT         |                             |
| created_at  | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP   |

### **Favorites** 
| Columna      | Tipo | Restricciones                     |
|-------------|------|-----------------------------------|
| id          | SERIAL | PRIMARY KEY                     |
| user_id     | INT  | FOREIGN KEY (Users.id)           |
| product_id  | INT  | FOREIGN KEY (Products.id)        |

### **Ingredients** 
| Columna       | Tipo         | Restricciones               |
|--------------|--------------|-----------------------------|
| id           | SERIAL       | PRIMARY KEY                 |
| name         | VARCHAR(100) | NOT NULL                    |
| allergen_info| TEXT         |                             |

### **Product_Ingredient** 
| Columna       | Tipo | Restricciones                     |
|--------------|------|-----------------------------------|
| product_id   | INT  | FOREIGN KEY (Products.id)        |
| ingredient_id| INT  | FOREIGN KEY (Ingredients.id)     |
| quantity     | DECIMAL(5,2)|                             |

### **Order_Status** 
| Columna      | Tipo         | Restricciones               |
|-------------|--------------|-----------------------------|
| id          | SERIAL       | PRIMARY KEY                 |
| status_name | VARCHAR(50)  | NOT NULL (e.g., 'pending', 'shipped', 'delivered') |

### **Addresses** 
| Columna      | Tipo         | Restricciones               |
|-------------|--------------|-----------------------------|
| id          | SERIAL       | PRIMARY KEY                 |
| user_id     | INT          | FOREIGN KEY (Users.id)      |
| street      | VARCHAR(100) | NOT NULL                    |
| city        | VARCHAR(50)  | NOT NULL                    |
| state       | VARCHAR(50)  | NOT NULL                    |
| zip_code    | VARCHAR(20)  | NOT NULL                    |
| is_default  | BOOLEAN      | DEFAULT FALSE               |


# Conexiones entre Tablas

## **Relaciones Principales**

1. **Usuarios**
    - `Users.address_id` → `Addresses.id` (Dirección principal del usuario)
    - `Users.id` ← `Orders.user_id` (Órdenes del usuario)
    - `Users.id` ← `Reviews.user_id` (Reseñas del usuario)
    - `Users.id` ← `Favorites.user_id` (Favoritos del usuario)

2. **Productos**
    - `Products.id` ← `Product_Category.product_id` (Categorías del producto)
    - `Products.id` ← `Order_Details.product_id` (Detalles de órdenes)
    - `Products.id` ← `Product_Images.product_id` (Imágenes del producto)
    - `Products.id` ← `Product_Options.product_id` (Opciones del producto)
    - `Products.id` ← `Product_Ingredient.product_id` (Ingredientes del producto)
    - `Products.id` ← `Promotion_Targets.target_id` (Promociones aplicadas)

3. **Categorías**
    - `Categories.id` ← `Product_Category.category_id` (Productos en la categoría)
    - `Categories.parent_id` → `Categories.id` (Categorías padre/hijo)
    - `Categories.id` ← `Promotion_Targets.target_id` (Promociones por categoría)

4. **Órdenes**
    - `Orders.id` ← `Order_Details.order_id` (Detalles de la orden)
    - `Orders.id` ← `Payments.order_id` (Pagos asociados)
    - `Orders.status_id` → `Order_Status.id` (Estado de la orden)

5. **Detalles de Órdenes**
    - `Order_Details.id` ← `Order_Options.order_detail_id` (Opciones seleccionadas)

6. **Promociones**
    - `Promotions.id` ← `Promotion_Targets.promotion_id` (Destino de la promoción)

7. **Ingredientes**
    - `Ingredients.id` ← `Product_Ingredient.ingredient_id` (Ingredientes en productos)

---

## **Relaciones de Muchos a Muchos**

- **Productos y Categorías**  
  `Product_Category` (product_id → Products.id, category_id → Categories.id)

- **Promociones y Destinos**  
  `Promotion_Targets` (promotion_id → Promotions.id, target_id → Products/Categories.id)

- **Productos e Imágenes**  
  `Product_Images` (product_id → Products.id)

- **Productos y Opciones**  
  `Product_Options` (product_id → Products.id)  
  `Order_Options` (option_id → Product_Options.id)

- **Productos e Ingredientes**  
  `Product_Ingredient` (product_id → Products.id, ingredient_id → Ingredients.id)

---

## **Relaciones de Configuración**

- **Estados de Órdenes**  
  `Order_Status.id` → `Orders.status_id`

- **Direcciones de Usuarios**  
  `Addresses.user_id` → `Users.id`  
  `Users.address_id` → `Addresses.id` (dirección principal)

- **Reseñas y Favoritos**  
  `Reviews.product_id` → `Products.id`  
  `Favorites.product_id` → `Products.id`

- **Opciones Personalizadas**  
  `Product_Options.id` → `Order_Options.option_id`