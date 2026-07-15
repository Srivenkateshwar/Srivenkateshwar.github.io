# Srivenkateshwar

<div style="background:#232B3E;border-radius:16px;padding:28px 20px 8px;margin:20px 0;">
<style>
@media (prefers-reduced-motion: no-preference) {
  @keyframes hm-spin  { to { transform: rotate(360deg); } }
  @keyframes hm-spinR { to { transform: rotate(-360deg); } }
  @keyframes hm-arm   { 0%,100% { transform: rotate(-14deg); } 50% { transform: rotate(10deg); } }
  @keyframes hm-fore  { 0%,100% { transform: rotate(20deg); } 50% { transform: rotate(-8deg); } }
  @keyframes hm-blink { 0%, 78%, 100% { opacity: 1; } 82%, 96% { opacity: 0.15; } }
  @keyframes hm-spark { 0%, 100% { opacity: 0; } 45%, 55% { opacity: 1; } }
  .hm-gear-big   { animation: hm-spin 9s linear infinite; }
  .hm-gear-small { animation: hm-spinR 6s linear infinite; }
  .hm-arm-upper  { animation: hm-arm 4s ease-in-out infinite; }
  .hm-arm-fore   { animation: hm-fore 4s ease-in-out infinite; }
  .hm-beacon     { animation: hm-blink 3s linear infinite; }
  .hm-spark      { animation: hm-spark 4s ease-in-out infinite; }
}
.hm-gear-big   { transform-origin: 150px 150px; }
.hm-gear-small { transform-origin: 258px 96px; }
.hm-arm-upper  { transform-origin: 470px 240px; }
.hm-arm-fore   { transform-origin: 470px 130px; }
</style>
<svg viewBox="0 0 600 320" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Animated gears and a robot arm at work — the site is being built" style="width:100%;max-width:460px;height:auto;display:block;margin:0 auto;">
  <line x1="30" y1="286" x2="570" y2="286" stroke="#3B4459" stroke-width="2" stroke-linecap="round"/>
  <g class="hm-gear-big">
    <g fill="#3B4459">
      <rect x="141" y="70" width="18" height="26" rx="4"/>
      <rect x="141" y="204" width="18" height="26" rx="4"/>
      <rect x="70" y="141" width="26" height="18" rx="4"/>
      <rect x="204" y="141" width="26" height="18" rx="4"/>
      <rect x="141" y="70" width="18" height="26" rx="4" transform="rotate(45 150 150)"/>
      <rect x="141" y="204" width="18" height="26" rx="4" transform="rotate(45 150 150)"/>
      <rect x="70" y="141" width="26" height="18" rx="4" transform="rotate(45 150 150)"/>
      <rect x="204" y="141" width="26" height="18" rx="4" transform="rotate(45 150 150)"/>
    </g>
    <circle cx="150" cy="150" r="62" fill="#2C3549" stroke="#4A5470" stroke-width="3"/>
    <circle cx="150" cy="150" r="20" fill="#232B3E" stroke="#4A5470" stroke-width="3"/>
  </g>
  <g class="hm-gear-small">
    <g fill="#4A5470">
      <rect x="252" y="52" width="12" height="18" rx="3"/>
      <rect x="252" y="122" width="12" height="18" rx="3"/>
      <rect x="214" y="90" width="18" height="12" rx="3"/>
      <rect x="284" y="90" width="18" height="12" rx="3"/>
      <rect x="252" y="52" width="12" height="18" rx="3" transform="rotate(45 258 96)"/>
      <rect x="252" y="122" width="12" height="18" rx="3" transform="rotate(45 258 96)"/>
      <rect x="214" y="90" width="18" height="12" rx="3" transform="rotate(45 258 96)"/>
      <rect x="284" y="90" width="18" height="12" rx="3" transform="rotate(45 258 96)"/>
    </g>
    <circle cx="258" cy="96" r="34" fill="#323C54" stroke="#5A6584" stroke-width="3"/>
    <circle cx="258" cy="96" r="11" fill="#232B3E" stroke="#5A6584" stroke-width="3"/>
  </g>
  <rect x="428" y="258" width="84" height="20" rx="6" fill="#3B4459"/>
  <rect x="452" y="234" width="36" height="28" rx="6" fill="#4A5470"/>
  <g class="hm-arm-upper">
    <rect x="458" y="126" width="24" height="120" rx="10" fill="#5A6584"/>
    <circle cx="470" cy="240" r="15" fill="#2C3549" stroke="#7A85A4" stroke-width="3"/>
    <g class="hm-arm-fore">
      <rect x="360" y="118" width="118" height="22" rx="10" fill="#7A85A4"/>
      <circle cx="470" cy="130" r="12" fill="#2C3549" stroke="#9AA5C0" stroke-width="3"/>
      <path d="M362 118 L340 104 M362 140 L340 152" stroke="#9AA5C0" stroke-width="8" stroke-linecap="round" fill="none"/>
      <g class="hm-spark">
        <path d="M332 122 l-10 -8 M330 130 l-13 1 M333 138 l-10 9" stroke="#E8734F" stroke-width="3" stroke-linecap="round"/>
        <circle cx="336" cy="129" r="4" fill="#E8734F"/>
      </g>
    </g>
  </g>
  <circle class="hm-beacon" cx="470" cy="228" r="4.5" fill="#E8734F"/>
  <g fill="#2C3549" stroke="#4A5470" stroke-width="2">
    <rect x="60" y="262" width="44" height="22" rx="4"/>
    <rect x="108" y="262" width="44" height="22" rx="4"/>
    <rect x="84" y="238" width="44" height="22" rx="4"/>
  </g>
</svg>
<p style="text-align:center;color:#E8734F;font-size:13px;letter-spacing:0.14em;text-transform:uppercase;padding-bottom:14px;margin-top:10px;">● Site under development</p>
</div>

Gears are turning, the robot arm is on it. The blog, however, is already live — head over to the first post:

**[→ Introduction to Vision-Language-Action Models](./blog/vla-introduction.md)**

---

*Find me on [GitHub](https://github.com/Srivenkateshwar).*