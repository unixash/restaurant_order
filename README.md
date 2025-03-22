import React, { useState } from "react";
import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";
import { motion } from "framer-motion";

const menuItems = [
  { id: 1, name: "Margherita Pizza", price: 199, category: "Pizza" },
  { id: 2, name: "Farmhouse Pizza", price: 249, category: "Pizza" },
  { id: 3, name: "Veg Burger", price: 149, category: "Burger" },
  { id: 4, name: "French Fries", price: 99, category: "Snacks" },
  { id: 5, name: "Coke (500ml)", price: 49, category: "Drinks" },
];

export default function RestaurantOrder() {
  const [cart, setCart] = useState({});
  const [category, setCategory] = useState("All");
  const [search, setSearch] = useState("");

  const addToCart = (item) => {
    setCart((prevCart) => ({
      ...prevCart,
      [item.id]: prevCart[item.id] ? prevCart[item.id] + 1 : 1,
    }));
  };

  const removeFromCart = (item) => {
    setCart((prevCart) => {
      const updatedCart = { ...prevCart };
      if (updatedCart[item.id] > 1) {
        updatedCart[item.id] -= 1;
      } else {
        delete updatedCart[item.id];
      }
      return updatedCart;
    });
  };

  const getTotal = () => {
    return Object.entries(cart).reduce((total, [id, qty]) => {
      const item = menuItems.find((item) => item.id === parseInt(id));
      return total + item.price * qty;
    }, 0);
  };

  const handleOrder = () => {
    const orderSummary = Object.entries(cart)
      .map(([id, qty]) => {
        const item = menuItems.find((item) => item.id === parseInt(id));
        return `${item.name} x${qty}`;
      })
      .join("%0A");

    const whatsappLink = `https://wa.me/919284582662?text=Order%20Summary%3A%0A${orderSummary}%0ATotal%3A%20₹${getTotal()}`;
    window.open(whatsappLink, "_blank");
  };

  return (
    <div className="p-4 min-h-screen bg-gradient-to-r from-blue-900 to-black text-white">
      <h1 className="text-4xl font-extrabold text-center mb-6 text-blue-400 drop-shadow-lg animate-pulse">
        Delicious Bites
      </h1>
      
      <div className="flex justify-center space-x-4 mb-4">
        {["All", "Pizza", "Burger", "Snacks", "Drinks"].map((cat) => (
          <Button
            key={cat}
            variant={category === cat ? "default" : "outline"}
            className="transition-transform transform hover:scale-110 bg-blue-700 hover:bg-blue-500 text-white border-none shadow-lg"
            onClick={() => setCategory(cat)}
          >
            {cat}
          </Button>
        ))}
      </div>

      <input
        type="text"
        placeholder="Search..."
        className="block w-full p-2 mb-4 bg-gray-800 text-white border border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400"
        onChange={(e) => setSearch(e.target.value)}
      />

      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        {menuItems
          .filter((item) => category === "All" || item.category === category)
          .filter((item) => item.name.toLowerCase().includes(search.toLowerCase()))
          .map((item) => (
            <motion.div
              key={item.id}
              whileHover={{ scale: 1.05 }}
              className="bg-gray-900 p-4 rounded-xl shadow-xl border border-blue-500 hover:shadow-blue-500/50"
            >
              <h2 className="text-2xl font-bold text-blue-300">{item.name}</h2>
              <p className="text-lg text-green-400">₹{item.price}</p>
              <div className="flex mt-3 space-x-2">
                <Button variant="outline" className="bg-red-500 text-white" onClick={() => removeFromCart(item)}>
                  -
                </Button>
                <span className="text-lg font-bold">{cart[item.id] || 0}</span>
                <Button variant="outline" className="bg-green-500 text-white" onClick={() => addToCart(item)}>
                  +
                </Button>
              </div>
            </motion.div>
          ))}
      </div>

      {Object.keys(cart).length > 0 && (
        <motion.div 
          className="fixed bottom-4 right-4 bg-gray-800 p-4 rounded-lg shadow-lg border border-blue-500 hover:shadow-blue-500/50"
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
        >
          <h2 className="text-lg font-bold text-blue-300">Order Summary</h2>
          <ul>
            {Object.entries(cart).map(([id, qty]) => {
              const item = menuItems.find((item) => item.id === parseInt(id));
              return (
                <li key={id} className="text-sm text-white">
                  {item.name} x{qty}
                </li>
              );
            })}
          </ul>
          <p className="mt-2 font-semibold text-green-400">Total: ₹{getTotal()}</p>
          <Button className="mt-2 w-full bg-green-500 hover:bg-green-600 text-white shadow-lg" onClick={handleOrder}>
            Place Order
          </Button>
        </motion.div>
      )}
    </div>
  );
}
