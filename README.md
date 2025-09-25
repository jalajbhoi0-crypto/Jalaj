<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Book My Salon</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
<style>
body { background: #f8f9fa; }
.nav-tabs .nav-link.active { background: #0d6efd; color: #fff; }
.card { margin-bottom: 15px; border-radius: 10px; overflow: hidden; }
.card img { width: 100%; height: 180px; object-fit: cover; }
.service-banner img { width: 32%; margin-right: 2%; height: 150px; object-fit: cover; border-radius: 10px; cursor: pointer; transition: 0.3s; }
.service-banner img:hover { transform: scale(1.05); }
.star { color: gold; font-size: 16px; }
@media (max-width: 576px) { .service-banner img { width: 100%; margin-bottom: 10px; } }
</style>
</head>
<body>

<div class="container py-4">
  <h2 class="text-center mb-4">💇 Book My Salon</h2>

  <ul class="nav nav-tabs mb-3" id="mainTabs" role="tablist">
    <li class="nav-item"><a class="nav-link active" data-bs-toggle="tab" href="#customer">Customer</a></li>
    <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#owner">Salon Owner</a></li>
    <li class="nav-item"><a class="nav-link" data-bs-toggle="tab" href="#admin">Admin</a></li>
  </ul>

  <div class="tab-content">
    <!-- Customer Tab -->
    <div class="tab-pane fade show active" id="customer">
      <h4>Popular Services</h4>
      <div class="service-banner mb-3">
        <img src="https://images.pexels.com/photos/3992873/pexels-photo-3992873.jpeg?auto=compress&cs=tinysrgb&h=350" alt="Haircut" onclick="filterByService('Haircut')">
        <img src="https://images.pexels.com/photos/4256259/pexels-photo-4256259.jpeg?auto=compress&cs=tinysrgb&h=350" alt="Facial" onclick="filterByService('Facial')">
        <img src="https://images.pexels.com/photos/4056532/pexels-photo-4056532.jpeg?auto=compress&cs=tinysrgb&h=350" alt="Massage" onclick="filterByService('Massage')">
      </div>
      <input id="searchService" class="form-control mb-3" placeholder="Search service...">
      <div id="salonList" class="row"></div>
    </div>

    <!-- Salon Owner Tab (Demo) -->
    <div class="tab-pane fade" id="owner">
      <h4>Salon Owner Panel (Demo)</h4>
      <p>This is a static demo for GitHub Pages.</p>
    </div>

    <!-- Admin Tab (Demo) -->
    <div class="tab-pane fade" id="admin">
      <h4>Admin Panel (Demo)</h4>
      <p>This is a static demo for GitHub Pages.</p>
    </div>
  </div>
</div>

<!-- Booking Modal -->
<div class="modal fade" id="bookingModal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header"><h5 class="modal-title">Book Service</h5><button type="button" class="btn-close" data-bs-dismiss="modal"></button></div>
      <div class="modal-body">
        <input type="text" id="customerName" class="form-control mb-2" placeholder="Your Name">
        <input type="text" id="customerPhone" class="form-control mb-2" placeholder="Phone Number">
        <input type="text" id="customerLocation" class="form-control mb-2" placeholder="Location">
        <input type="date" id="bookingDate" class="form-control mb-2">
        <select id="availableSlots" class="form-control mb-2"></select>
        <input type="text" id="bookingService" class="form-control mb-2" placeholder="Service Name">
      </div>
      <div class="modal-footer"><button class="btn btn-success" onclick="confirmBooking()">Confirm Booking</button></div>
    </div>
  </div>
</div>

<!-- Review Modal -->
<div class="modal fade" id="reviewModal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header"><h5 class="modal-title">Leave a Review</h5><button type="button" class="btn-close" data-bs-dismiss="modal"></button></div>
      <div class="modal-body">
        <input type="text" id="reviewerName" class="form-control mb-2" placeholder="Your Name">
        <select id="reviewRating" class="form-control mb-2">
          <option value="5">5 - Excellent</option>
          <option value="4">4 - Good</option>
          <option value="3">3 - Average</option>
          <option value="2">2 - Poor</option>
          <option value="1">1 - Terrible</option>
        </select>
        <textarea id="reviewComment" class="form-control mb-2" placeholder="Write your review..."></textarea>
      </div>
      <div class="modal-footer"><button class="btn btn-success" onclick="submitReview()">Submit Review</button></div>
    </div>
  </div>
</div>

<script>
// -------- Mock Salon Data --------
let salons = [
  { _id:"1", name:"Luxury Hair Salon", email:"lux@example.com", subscription:{active:true}, photos:["https://images.pexels.com/photos/3992873/pexels-photo-3992873.jpeg?auto=compress&cs=tinysrgb&h=350"], services:[{name:"Haircut", price:250, photo:"https://images.pexels.com/photos/3992873/pexels-photo-3992873.jpeg?auto=compress&cs=tinysrgb&h=350"}], reviews:[{name:"John", rating:5, comment:"Great haircut!"}], avgRating:5 }
];

// -------- Display Salons --------
function loadSalons(){
  let list = document.getElementById("salonList");
  list.innerHTML="";
  salons.forEach(s=>{
    let salonPhotos = s.photos.map(p=>`<img src="${p}" class="img-thumbnail me-1" style="width:80px;height:80px;object-fit:cover;">`).join("");
    let servicesList = "<ul>"+s.services.map(x=>`<li>${x.name} - ₹${x.price}${x.photo?'<br><img src="'+x.photo+'" style="width:100px;height:80px;object-fit:cover;">':''}</li>`).join('')+"</ul>";
    let reviewsList = "<ul>"+s.reviews.map(r=>`<li><b>${r.name}:</b> ${r.comment} (${r.rating}⭐)</li>`).join('')+"</ul>";
    let card = document.createElement("div");
    card.className="col-md-4";
    card.innerHTML=`<div class="card p-3 shadow-sm">
      <h5>${s.name}</h5>
      <p><b>Email:</b> ${s.email}</p>
      ${salonPhotos}
      ${servicesList}
      <div><b>Rating:</b> ${s.avgRating}⭐</div>
      ${reviewsList}
      <button class="btn btn-success mb-2" onclick="openBooking('${s._id}')">Book</button>
      <button class="btn btn-primary btn-sm" onclick="openReviewModal('${s._id}')">Add Review</button>
    </div>`;
    list.appendChild(card);
  });
}

// -------- Booking Functions --------
let currentSalonId="";
function openBooking(id){ currentSalonId=id; document.getElementById("bookingService").value=""; new bootstrap.Modal(document.getElementById('bookingModal')).show(); }
function confirmBooking(){ alert("Booking confirmed! (Demo)"); bootstrap.Modal.getInstance(document.getElementById('bookingModal')).hide(); }

// -------- Review Functions --------
function openReviewModal(id){ currentSalonId=id; document.getElementById("reviewerName").value=""; document.getElementById("reviewComment").value=""; document.getElementById("reviewRating").value="5"; new bootstrap.Modal(document.getElementById('reviewModal')).show(); }
function submitReview(){
  let name=document.getElementById("reviewerName").value;
  let rating=parseInt(document.getElementById("reviewRating").value);
  let comment=document.getElementById("reviewComment").value;
  if(!name||!comment){ alert("All fields required!"); return; }
  let salon=salons.find(s=>s._id===currentSalonId);
  salon.reviews.push({name,rating,comment});
  salon.avgRating=(salon.reviews.reduce((sum,r)=>sum+r.rating
