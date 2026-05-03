{
  "name": "pines-crossing-golf",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "14.0.0",
    "react": "^18",
    "react-dom": "^18",
    "@supabase/supabase-js": "^2.0.0",
    "stripe": "^14.0.0"
  }
}
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body style={{ margin: 0, fontFamily: "Arial" }}>
        {children}
      </body>
    </html>
  );
}
import Link from "next/link";

export default function Home() {
  return (
    <div style={{ padding: 40 }}>
      <h1>🏌️ Pines Crossing Golf Club</h1>
      <p>Book tee times instantly online</p>

      <Link href="/book">
        <button style={{ marginTop: 20, padding: 10 }}>
          Book Tee Time
        </button>
      </Link>
    </div>
  );
}
"use client";

import { useState } from "react";

const times = [
  "7:00 AM","7:30 AM","8:00 AM","8:30 AM",
  "3:00 PM","3:30 PM","4:00 PM","4:30 PM"
];

export default function Book() {
  const [selected, setSelected] = useState("");

  async function bookTime() {
    const res = await fetch("/api/book", {
      method: "POST",
      body: JSON.stringify({ time: selected })
    });

    const data = await res.json();
    alert(data.message);
  }

  return (
    <div style={{ padding: 40 }}>
      <h2>Book Tee Time</h2>

      <div style={{ display: "flex", flexWrap: "wrap", gap: 10 }}>
        {times.map(t => (
          <button
            key={t}
            onClick={() => setSelected(t)}
            style={{
              padding: 10,
              background: selected === t ? "green" : "#eee"
            }}
          >
            {t}
          </button>
        ))}
      </div>

      <button
        onClick={bookTime}
        style={{ marginTop: 20, padding: 10 }}
      >
        Confirm Booking
      </button>
    </div>
  );
}
export default function Admin() {
  return (
    <div style={{ padding: 40 }}>
      <h1>Admin Dashboard</h1>
      <p>Manage bookings, tee times, and users (placeholder UI)</p>
    </div>
  );
}
import { createClient } from "@supabase/supabase-js";

export const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY
);
import Stripe from "stripe";

export const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);
export async function POST(req) {
  const { time } = await req.json();

  return Response.json({
    message: `✅ Tee time booked for ${time}`
  });
}
import { stripe } from "@/lib/stripe";

export async function POST() {
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"],
    mode: "payment",
    line_items: [
      {
        price_data: {
          currency: "usd",
          product_data: {
            name: "Pines Crossing Tee Time"
          },
          unit_amount: 5000
        },
        quantity: 1
      }
    ],
    success_url: "http://localhost:3000",
    cancel_url: "http://localhost:3000"
  });

  return Response.json({ url: session.url });
}
export async function POST() {
  return new Response("Webhook received");
}
NEXT_PUBLIC_SUPABASE_URL=your_url_here
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_key_here

SUPABASE_SERVICE_ROLE_KEY=your_key_here

STRIPE_SECRET_KEY=your_key_here
