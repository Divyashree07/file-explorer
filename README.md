import React from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import ProfileList from './components/ProfileList';
import ProfileDetail from './components/ProfileDetail';

function App() {
  return (
    <Router>
      <div>
        <Switch>
          <Route exact path="/" component={ProfileList} />
          <Route path="/profile/:id" component={ProfileDetail} />
        </Switch>
      </div>
    </Router>
  );
}

export default App;
import React, { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import axios from 'axios';

function ProfileList() {
  const [profiles, setProfiles] = useState([]);

  useEffect(() => {
    axios.get('/api/profiles')
      .then(response => {
        setProfiles(response.data);
      });
  }, []);

  return (
    <div>
      <h1>Profile List</h1>
      <ul>
        {profiles.map(profile => (
          <li key={profile.id}>
            <Link to={`/profile/${profile.id}`}>{profile.name}</Link>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default ProfileList;
import React, { useState, useEffect } from 'react';
import { useParams } from 'react-router-dom';
import axios from 'axios';
import { GoogleMap, LoadScript, Marker } from '@react-google-maps/api';

function ProfileDetail() {
  const { id } = useParams();
  const [profile, setProfile] = useState(null);

  useEffect(() => {
    axios.get(`/api/profiles/${id}`)
      .then(response => {
        setProfile(response.data);
      });
  }, [id]);

  if (!profile) {
    return <div>Loading...</div>;
  }

  const mapContainerStyle = {
    height: "400px",
    width: "100%"
  };

  const center = {
    lat: profile.address.lat,
    lng: profile.address.lng
  };

  return (
    <div>
      <h1>{profile.name}</h1>
      <p>{profile.bio}</p>
      <LoadScript googleMapsApiKey="YOUR_GOOGLE_MAPS_API_KEY">
        <GoogleMap
          mapContainerStyle={mapContainerStyle}
          center={center}
          zoom={10}
        >
          <Marker position={center} />
        </GoogleMap>
      </LoadScript>
    </div>
  );
}

export default ProfileDetail;
