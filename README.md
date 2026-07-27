# ⚡ Testing-Guide-Part-2 (Database Testing)

This project demonstrates the fundamentals of **Database Testing in Laravel 10** using **Factories, Seeders, Refresh Database, Transactions, and Database Assertions**.

Database testing ensures that application data is created, stored, updated, and deleted correctly. Laravel provides built-in testing tools to generate fake data, reset databases, and verify database operations automatically.

Instead of manually checking database records after every code change, automated database testing validates application behavior quickly and reliably.

---

# ❓ Why Use Database Testing?

Database testing is useful when:

- You want to verify database operations automatically.
- You want realistic test data.
- You want clean databases for every test.
- You want to test models and relationships.
- You want safer application changes.

Common examples:

- User Registration Testing
- Login Testing
- Product CRUD Testing
- Order Processing Testing
- API Database Testing
- Relationship Testing

---

## Without Database Testing

```
Code Change
      │
      ▼
Manual Database Checking
      │
      ▼
Possible Human Error
```

---

## With Database Testing

```
Code Change
      │
      ▼
Automated Database Test
      │
      ▼
Database Verification
      │
      ▼
PASS / FAIL
```

---

# 🧩 Database Testing Concepts Covered

✅ Laravel Factories  
✅ Laravel Seeders  
✅ RefreshDatabase  
✅ Database Transactions  
✅ Database Assertions  

---

# 1️⃣ Laravel Factories

## What are Factories?

Factories generate fake database records automatically for testing.

Instead of manually creating:

```
Name
Email
Password
Address
```

Factories create realistic dummy data automatically.

Example:

```
John Smith
john@test.com
password123
```

---

## Create Factory

Generate a factory:

```bash
php artisan make:factory UserFactory
```

Creates:

```
database/factories/UserFactory.php
```

---

## Factory Example

```php
namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class UserFactory extends Factory
{
    public function definition(): array
    {
        return [

            'name' => fake()->name(),

            'email' => fake()->unique()->safeEmail(),

            'password' => bcrypt('password'),

        ];
    }
}
```

---

## Using Factory in Test

Create a fake user:

```php
$user = User::factory()->create();
```

Create multiple records:

```php
User::factory(10)->create();
```

Result:

```
10 Fake Users Created
```

---

# 2️⃣ Laravel Seeders

## What are Seeders?

Seeders insert predefined data into the database.

Used for:

- Development Data
- Testing Data
- Demo Applications
- Initial Setup

---

## Create Seeder

```bash
php artisan make:seeder UserSeeder
```

Creates:

```
database/seeders/UserSeeder.php
```

---

## Seeder Example

```php
use App\Models\User;
use Illuminate\Database\Seeder;

class UserSeeder extends Seeder
{
    public function run(): void
    {
        User::factory(50)->create();
    }
}
```

---

## Register Seeder

Inside:

```
database/seeders/DatabaseSeeder.php
```

Add:

```php
public function run(): void
{
    $this->call([
        UserSeeder::class
    ]);
}
```

---

## Run Seeder

```bash
php artisan db:seed
```

or:

```bash
php artisan migrate:fresh --seed
```

---

# 3️⃣ Refresh Database

## What is RefreshDatabase?

`RefreshDatabase` resets the database before running tests.

It provides:

```
Delete Tables
      │
      ▼
Run Migrations
      │
      ▼
Fresh Database
```

---

## Example

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserTest extends TestCase
{

    use RefreshDatabase;


    public function test_user_creation()
    {

        $user = User::factory()->create();


        $this->assertDatabaseHas('users',[

            'id'=>$user->id

        ]);

    }

}
```

---

## Why Use RefreshDatabase?

Without RefreshDatabase:

```
Test 1
Create User

Test 2
Old Data Exists

Problem ❌
```

With RefreshDatabase:

```
Test 1
Fresh Database

Test 2
Fresh Database

Clean Testing ✅
```

---

# 4️⃣ Database Transactions

## What are Transactions?

Transactions allow database changes to rollback after testing.

Flow:

```
Start Transaction
        │
        ▼
Insert Data
        │
        ▼
Run Test
        │
        ▼
Rollback Changes
```

Database remains clean.

---

## Example

```php
use Illuminate\Foundation\Testing\DatabaseTransactions;


class UserTest extends TestCase
{

    use DatabaseTransactions;


    public function test_user_creation()
    {

        User::create([

            'name'=>'John',

            'email'=>'john@test.com'

        ]);


        $this->assertDatabaseHas('users',[

            'email'=>'john@test.com'

        ]);

    }

}
```

---

# 5️⃣ Database Assertions

Assertions verify database results.

---

## Check Record Exists

```php
$this->assertDatabaseHas('users',[

    'email'=>'test@test.com'

]);
```

---

## Check Record Missing

```php
$this->assertDatabaseMissing('users',[

    'email'=>'deleted@test.com'

]);
```

---

## Check Record Count

```php
$this->assertDatabaseCount(
    'users',
    10
);
```

---

# 🔥 Real World Example

## User Registration Testing

Application Flow:

```
User Opens Register Page
          │
          ▼
Submit Registration Form
          │
          ▼
Create User
          │
          ▼
Save Data
          │
          ▼
Verify Database
```

Test:

```php
public function test_user_registration()
{

    $response = $this->post('/register',[

        'name'=>'John',

        'email'=>'john@test.com',

        'password'=>'password',

        'password_confirmation'=>'password'

    ]);


    $this->assertDatabaseHas('users',[

        'email'=>'john@test.com'

    ]);

}
```

---

# 📋 Database Testing Flow

```
Create Factory Data
        │
        ▼
Run Test
        │
        ▼
Perform Database Action
        │
        ▼
Database Assertion
        │
        ▼
Refresh / Rollback
        │
        ▼
PASS / FAIL
```

---

# ⚖️ Database Testing Technique Comparison

| Technique | Purpose | When To Use | Example |
|-----------|---------|-------------|---------|
| Factories | Generate fake data | When tests require database records | Create fake users/products |
| Seeders | Insert predefined data | Development and demo data | Create admin account |
| RefreshDatabase | Reset database | Feature testing | Clean database before every test |
| Transactions | Rollback changes | Small database operations | Model testing |
| Assertions | Verify database state | Check stored data | Verify user exists |

---

# 🏗️ Real Project Testing Strategy

| Application Area | Recommended Technique |
|------------------|----------------------|
| User Testing | Factory + Assertions |
| Product Testing | Factory |
| Order Testing | Factory + Transactions |
| API Testing | RefreshDatabase |
| Demo Data | Seeders |
| CRUD Testing | Database Assertions |

---

# 📦 Useful Laravel Commands

## Create Factory

```bash
php artisan make:factory ProductFactory
```

## Create Seeder

```bash
php artisan make:seeder ProductSeeder
```

## Run Seeder

```bash
php artisan db:seed
```

## Fresh Database With Seeder

```bash
php artisan migrate:fresh --seed
```

## Create Test

```bash
php artisan make:test ProductTest
```

## Run Tests

```bash
php artisan test
```

---

# 📌 Features

- Laravel Factories
- Laravel Seeders
- Database Assertions
- RefreshDatabase Testing
- Database Transactions
- Automated Database Testing
- Fake Data Generation
- Clean Testing Environment

---

# 🎯 Key Takeaway

```
Factories
     |
     └── Generate Fake Data


Seeders
     |
     └── Insert Application Data


RefreshDatabase
     |
     └── Reset Database


Transactions
     |
     └── Rollback Changes


Assertions
     |
     └── Verify Results
```

Database testing helps Laravel applications become:

✅ More Reliable  
✅ Easier to Maintain  
✅ Safer to Update  
✅ Less Error-Prone
