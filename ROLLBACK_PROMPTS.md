# Rollback Prompts for Guest Mode Features

Future mein agar aap in features ko revert karna chahein to ye prompts use karein.

---

## 1. Scroll Feature Rollback (PriceListPage)

```
Revert the full-page scroll for guest standalone mode in price_list_page.dart:

Current (need to change):
- body uses `widget.standalone ? SingleChildScrollView(...) : Column(...)`
- `_buildTable()` method calls `_buildTableContent(shrinkWrap: shrinkWrap)`
- `_buildTableContent()` method exists with conditional scroll wrapping

Revert to original:
- body: replace whole conditional body with:
  ```
  Column(
    children: [
      if (widget.standalone) _buildComplaintCard(),
      _buildHeader(),
      _buildSearch(),
      _buildCategories(),
      _buildUpdateBanner(),
      Expanded(child: _buildTable()),
    ],
  ),
  ```
- Remove `_buildTableContent()` method entirely
- Restore `_buildTable()` to original:
  ```
  Widget _buildTable() {
    if (_loading) return const Center(...);
    if (_items.isEmpty) return const Center(...);
    return SingleChildScrollView(
      child: Padding(
        padding: EdgeInsets.fromLTRB(16, 10, 16, 0),
        child: Column(
          children: [
            Card(...),
            _buildDisclaimer(),
          ],
        ),
      ),
    );
  }
  ```
```

---

## 2. AppBar Login Icon Rollback

```
Revert the standalone AppBar in price_list_page.dart:

Current:
```
appBar: widget.standalone
    ? AppBar(
        backgroundColor: kPrimaryGreen,
        elevation: 0,
        automaticallyImplyLeading: false,
        titleSpacing: 0,
        title: Padding(
          padding: EdgeInsets.only(left: 8),
          child: Row(
            children: [
              GestureDetector(
                onTap: () => Navigator.pop(context),
                child: Icon(Icons.arrow_back_ios,
                    color: Colors.white, size: 20),
              ),
              SizedBox(width: 8),
              Text('Prices / قیمتیں',
                  style: TextStyle(
                      color: Colors.white,
                      fontSize: 18,
                      fontWeight: FontWeight.bold)),
            ],
          ),
        ),
        actions: [
          TextButton.icon(
            onPressed: () => ...,
            icon: Icon(Icons.login, color: Colors.white, size: 18),
            label: Text('Login / لاگ ان', ...),
          ),
        ],
      )
    : null,
```

Revert to:
```
appBar: widget.standalone ? const KarachiAppBar(showBack: true) : null,
```
Also remove the `import 'login_page.dart';` from the top of the file.
```

---

## 3. Complaint Card Rollback

```
Revert the complaint card in price_list_page.dart:

Primary change:
- The `if (widget.standalone) _buildComplaintCard()` line at top of body Column should be removed
- The `_buildComplaintCard()` method should be deleted entirely (last method in file)

If you want to keep it but move it back to bottom of price table:
  Instead of deleting, move `_buildComplaintCard()` call from body to inside `_buildTable()` 
  after `_buildDisclaimer()`.
```

---

## 4. "Browse as Guest" Button Rollback (login_page.dart)

```
Remove the guest button from login_page.dart:

1. Remove `import 'price_list_page.dart';` from top of file
2. Remove the following code that was added after the Register link (before SizedBox(height: 24) and _buildFooter()):

```
SizedBox(height: 16),

Row(
  children: [
    Expanded(child: Divider(color: Colors.grey.shade300)),
    Padding(
      padding: EdgeInsets.symmetric(horizontal: 12),
      child: Text('or / یا',
          style: TextStyle(color: Colors.grey, fontSize: 13)),
    ),
    Expanded(child: Divider(color: Colors.grey.shade300)),
  ],
),
SizedBox(height: 16),

SizedBox(
  width: double.infinity,
  height: 44,
  child: OutlinedButton.icon(
    onPressed: () {
      Navigator.push(
        context,
        MaterialPageRoute(
          builder: (_) => const PriceListPage(standalone: true),
        ),
      );
    },
    icon: Icon(Icons.visibility_outlined, color: primaryGreen),
    label: Text(
      'Browse as Guest / بطور مہمان دیکھیں',
      style: TextStyle(
        color: primaryGreen,
        fontWeight: FontWeight.w600,
        fontSize: 14,
      ),
    ),
    style: OutlinedButton.styleFrom(
      side: BorderSide(color: primaryGreen),
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(10),
      ),
    ),
  ),
),
SizedBox(height: 8),
Center(
  child: Text(
    'Full access ke liye login karein\nمکمل رسائی کے لیے لاگ ان کریں',
    textAlign: TextAlign.center,
    style: TextStyle(fontSize: 12, color: Colors.grey.shade600),
  ),
),
```

---

## 5. Complete Guest Mode Rollback (All Changes)

```
Revert the complete Guest Mode feature from the Flutter app:

### File: flutter/lib/pages/login_page.dart
1. Remove `import 'price_list_page.dart';`
2. Remove the "or / یا" divider, "Browse as Guest" button, and 
   instruction text added after the Register link.

### File: flutter/lib/pages/price_list_page.dart
1. Remove `import 'login_page.dart';`
2. Restore AppBar to: 
   `appBar: widget.standalone ? const KarachiAppBar(showBack: true) : null,`
3. Restore body to:
   ```
   Column(
     children: [
       _buildHeader(),
       _buildSearch(),
       _buildCategories(),
       _buildUpdateBanner(),
       Expanded(child: _buildTable()),
     ],
   ),
   ```
4. Remove the call to _buildComplaintCard() (remove `if (widget.standalone) _buildComplaintCard()`)
5. Remove _buildComplaintCard() method entirely
6. Restore _buildTable() to original simple version (no shrinkWrap, no _buildTableContent)
7. No other files affected.
```
