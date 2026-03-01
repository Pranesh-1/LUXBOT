import json
import random
import urllib.parse

categories = {
    "Bags": [
        "Neverfull", "Speedy", "Alma", "Capucines", "Coussin", "Dauphine", "Twist", "Onthego", "Pochette Metis", "Multi Pochette", "Keepall", "Soft Trunk", "Christopher"
    ],
    "Shoes": [
        "LV Trainer Sneaker", "Star Trail Ankle Boot", "Silhouette Ankle Boot", "Archlight Sneaker", "Pillow Boot", "Monogram Loafer", "Bom Dia Mule", "Run Away Sneaker", "Vendome Oxford", "Major Loafer"
    ],
    "Accessories": [
        "Monogram Classic Scarf", "Reykjavik Scarf", "LV Initiales Belt", "Daily Confidential Bracelet", "Essential V Necklace", "My Monogram Sunglasses", "Zippy Wallet", "Multiple Wallet", "Pocket Organizer", "Key Pouch"
    ],
    "Ready-to-Wear": [
        "Silk Monogram Pajama Shirt", "Monogram Denim Jacket", "Classic T-Shirt", "Reversible Bomber Jacket", "Monogram Jacquard Knit", "Wool Tailored Coat", "Leather Aviator", "Pleated Midi Skirt", "Monogram Board Shorts", "Cashmere Hoodie"
    ],
    "Watches & Jewelry": [
        "Tambour Street Diver Watch", "Tambour Horizon Smartwatch", "Volt Upside Down Pendant", "Empreinte Ring", "Color Blossom Bracelet", "Tambour Moon Dual Time", "Dentelle Diamond Ring"
    ]
}

materials = ["Monogram Canvas", "Damier Ebene", "Damier Azur", "Taurillon Leather", "Epi Leather", "Monogram Empreinte", "Mahina Leather", "Calf Leather", "Exotic Crocodile", "Cashmere", "Silk", "Wool"]
colors = ["Noir", "Cognac", "Rose Ballerine", "Bleu Marine", "Rouge", "Vert", "Blanc", "Anthracite", "Gold", "Silver", "Neon", "Classic Brown"]
sizes = ["BB", "PM", "MM", "GM", "Mini", "Micro", "Bandouliere 50", "Bandouliere 55"]

catalog = []
item_id_counter = 1

for _ in range(80):  # Kept to 80 to fit Groq limit
    cat = random.choice(list(categories.keys()))
    base_name = random.choice(categories[cat])
    
    if cat == "Bags" and random.random() > 0.3:
        name = f"{base_name} {random.choice(sizes)}"
    else:
        name = base_name

    mat = random.choice(materials)
    color = random.choice(colors)
    
    if cat == "Ready-to-Wear":
        mat = random.choice(["Cashmere", "Silk", "Wool", "Cotton", "Denim"])
    elif cat == "Watches & Jewelry":
        mat = random.choice(["18k Gold", "Stainless Steel", "White Gold", "Platinum"])
    
    price = random.randint(300, 8000)
    if mat in ["Exotic Crocodile", "18k Gold", "Platinum"]:
        price += random.randint(5000, 25000)

    desc = f"Luxurious {name} crafted from the finest {mat} in {color}. An iconic addition to your collection, embodying the spirit of Louis Vuitton."

    tags = [cat.lower(), mat.lower().replace(" ", "-"), color.lower(), "iconic", "luxury"]
    
    # Generate dummy URLs
    safe_name = urllib.parse.quote_plus(f"Louis Vuitton {name} {color}")
    image_url = f"https://dummyimage.com/600x600/f5f5dc/000000.jpg&text={safe_name}"
    product_url = f"https://us.louisvuitton.com/eng-us/search/{urllib.parse.quote_plus(name)}"
    
    item = {
        "id": f"lv_{item_id_counter:03d}",
        "name": name,
        "price": price,
        "currency": "USD",
        "category": cat,
        "description": desc,
        "color": color,
        "material": mat,
        "style_tags": tags,
        "image_url": image_url,
        "product_url": product_url
    }
    
    catalog.append(item)
    item_id_counter += 1

with open("C:\\Users\\Admin\\.gemini\\antigravity\\brain\\cbc1c264-694c-4e76-9b23-6bc00c6cf06d\\lv_catalog_advanced.json", "w") as f:
    json.dump(catalog, f, indent=2)

print("Advanced catalog created successfully!")

# Regenerate the Make payload file with the new Executive Assistant prompt
prompt = """Role: Your name is Laurent. You are an Executive Assistant and Senior Client Advisor at Louis Vuitton. 
Tone/Persona: Highly professional, luxurious, and exceedingly helpful. You can handle normal conversational queries (like scheduling advice, general knowledge, or polite banter) like a real human executive. 
When asked about fashion, styling, or gifts, you transition to your role as a Senior Client Advisor.
Constraints: 
1. When recommending products, ONLY use the catalog provided. Do not hallucinate.
2. ALWAYS provide the `product_url` and `image_url` for any item you recommend so the user can see it! Output the image URL on its own line so WhatsApp unfurls it.
3. Be concise (under 150 words usually) but extremely competent. 
Catalog Database:
"""

payload = {
    "model": "llama-3.3-70b-versatile",
    "messages": [
        {
            "role": "system",
            "content": prompt + json.dumps(catalog)
        },
        {
            "role": "user",
            "content": "INSERT_TWILIO_BODY_HERE"
        }
    ]
}

with open("C:\\Users\\Admin\\.gemini\\antigravity\\brain\\cbc1c264-694c-4e76-9b23-6bc00c6cf06d\\make_advanced_payload.json", "w") as f:
    json.dump(payload, f, indent=2)

print("Advanced Make payload stringified and saved.")
