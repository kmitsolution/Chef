**complete, step-by-step guide** for creating and executing your **first Chef cookbook with a custom recipe (`myrecipe.rb`)** — not `default.rb`.

---

##  Chef: Create and Run Your First Cookbook (with `myrecipe.rb`)

---

###  Prerequisites

Make sure you have:

* Chef Workstation installed → [Install guide](https://docs.chef.io/workstation/install_workstation/)
* A terminal with root or sudo access

---

##  Step 1: Create a Chef Repo

```bash
chef generate repo my-chef-repo
cd my-chef-repo
```

Chef will create this structure:

```
my-chef-repo/
├── cookbooks/
├── data_bags/
├── environments/
├── roles/
└── ...
```

---

##  Step 2: Generate a Cookbook

Navigate to the `cookbooks/` directory:

```bash
cd cookbooks
chef generate cookbook test_cookbook
```

This creates:

```
test_cookbook/
├── recipes/
│   └── default.rb
├── metadata.rb
├── README.md
...
```

---

##  Step 3: Create Your Custom Recipe (`myrecipe.rb`)

Create a new recipe:

```bash
touch test_cookbook/recipes/myrecipe.rb
```

Or use Chef command:

```bash
chef generate recipe test_cookbook myrecipe
```

Now edit `recipes/myrecipe.rb`:

```ruby
#
# Cookbook:: test_cookbook
# Recipe:: myrecipe
#

file '/tmp/hello_from_myrecipe.txt' do
  content 'Hello, world! This is my first custom Chef recipe.'
  mode '0644'
  owner 'root'
  group 'root'
end
```

This will create a file on the system when the recipe runs.

---

##  Step 4: Run the Custom Recipe Locally

Navigate back to the root of the Chef repo:

```bash
cd ../../  # back to my-chef-repo
```

Run Chef in local mode:

```bash
sudo chef-client --local-mode --runlist 'recipe[test_cookbook::myrecipe]'
```

 `::` is used to specify the recipe inside the cookbook.

You should see output like:

```
...
Recipe: test_cookbook::myrecipe
  * file[/tmp/hello_from_myrecipe.txt] action create
...
```

### Now check the result:

```bash
cat /tmp/hello_from_myrecipe.txt
# Output: Hello, world! This is my first custom Chef recipe.
```

---

## 📁 Final Project Structure

```
my-chef-repo/
├── cookbooks/
│   └── test_cookbook/
│       ├── recipes/
│       │   ├── default.rb
│       │   └── myrecipe.rb      👈 your custom recipe
│       ├── metadata.rb
│       └── README.md
```

---

## 🧼 (Optional) Clean up

If you want to remove the test file created:

```bash
rm /tmp/hello_from_myrecipe.txt
```

---

## ✅ Recap: Full Commands List

```bash
# 1. Create repo
chef generate repo my-chef-repo
cd my-chef-repo

# 2. Create cookbook
cd cookbooks
chef generate cookbook test_cookbook

# 3. Create custom recipe
chef generate recipe test_cookbook myrecipe

# 4. Add code to test_cookbook/recipes/myrecipe.rb

# 5. Run the recipe
cd ../../
sudo chef-client --local-mode --runlist 'recipe[test_cookbook::myrecipe]'
```


