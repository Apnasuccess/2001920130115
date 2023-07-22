..................................................................alltrainpage.js......................................................
import React, { useEffect, useState } from 'react';
import { Link } from 'react-router-dom';
import { Typography, Table, TableBody, TableCell, TableContainer, TableHead, TableRow, Paper } from '@mui/material';

const AllTrainsPage = ({ authHeader }) => {
  const [trains, setTrains] = useState([]);

  useEffect(() => {
    fetchAllTrains();
  }, []);

  const fetchAllTrains = async () => {
    try {
      const response = await fetch('http://20.244.56.144/train/trains', {
        headers: {
          Authorization: authHeader,
        },
      });
      const data = await response.json();
      setTrains(data);
    } catch (error) {
      console.error('Error fetching trains:', error);
    }
  };

  return (
    <div>
      <Typography variant="h5">All Trains Schedule</Typography>
      <TableContainer component={Paper}>
        <Table>
          <TableHead>
            <TableRow>
              <TableCell>Train Name</TableCell>
              <TableCell>Train Number</TableCell>
              <TableCell>Departure Time</TableCell>
              <TableCell>Seat Availability (Sleeper/AC)</TableCell>
              <TableCell>Price (Sleeper/AC)</TableCell>
              <TableCell>Delay (Minutes)</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {trains.map((train) => (
              <TableRow key={train.trainNumber}>
                <TableCell>{train.trainName}</TableCell>
                <TableCell>{train.trainNumber}</TableCell>
                <TableCell>{`${train.departureTime.Hours}:${train.departureTime.Minutes}:${train.departureTime.Seconds}`}</TableCell>
                <TableCell>{`${train.seatsAvailable.sleeper}/${train.seatsAvailable.AC}`}</TableCell>
                <TableCell>{`${train.price.sleeper}/${train.price.AC}`}</TableCell>
                <TableCell>{train.delayedBy}</TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </TableContainer>
    </div>
  );
};

export default AllTrainsPage;
..................................................................singletrainpage.js...........................................................
import React, { useEffect, useState } from 'react';
import { useParams } from 'react-router-dom';
import { Typography, Table, TableBody, TableCell, TableContainer, TableHead, TableRow, Paper } from '@mui/material';

const SingleTrainPage = ({ authHeader }) => {
  const { trainNumber } = useParams();
  const [train, setTrain] = useState(null);

  useEffect(() => {
    fetchSingleTrain();
  }, []);

  const fetchSingleTrain = async () => {
    try {
      const response = await fetch(`http://20.244.56.144/train/trains/${trainNumber}`, {
        headers: {
          Authorization: authHeader,
        },
      });
      const data = await response.json();
      setTrain(data);
    } catch (error) {
      console.error('Error fetching single train:', error);
    }
  };

  if (!train) {
    return <div>Loading...</div>;
  }

  return (
    <div>
      <Typography variant="h5">Single Train Details</Typography>
      <TableContainer component={Paper}>
        <Table>
          <TableHead>
            <TableRow>
              <TableCell>Train Name</TableCell>
              <TableCell>Train Number</TableCell>
              <TableCell>Departure Time</TableCell>
              <TableCell>Seat Availability (Sleeper/AC)</TableCell>
              <TableCell>Price (Sleeper/AC)</TableCell>
              <TableCell>Delay (Minutes)</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            <TableRow>
              <TableCell>{train.trainName}</TableCell>
              <TableCell>{train.trainNumber}</TableCell>
              <TableCell>{`${train.departureTime.Hours}:${train.departureTime.Minutes}:${train.departureTime.Seconds}`}</TableCell>
              <TableCell>{`${train.seatsAvailable.sleeper}/${train.seatsAvailable.AC}`}</TableCell>
              <TableCell>{`${train.price.sleeper}/${train.price.AC}`}</TableCell>
              <TableCell>{train.delayedBy}</TableCell>
            </TableRow>
          </TableBody>
        </Table>
      </TableContainer>
    </div>
  );
};

export default SingleTrainPage;
